---
title: Настройка группы доступности AlwaysOn для SQL Server на виртуальной Машине Azure с помощью CLI виртуальной Машины SQL Azure
description: 'С помощью Azure CLI для создания отказоустойчивого кластера Windows, прослушиватель группы доступности и внутренней подсистемы балансировки нагрузки на виртуальную Машину SQL Server в Azure. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1c5c5f4c8125f801edc89d47851871d8eb06a2f9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762877"
---
# <a name="use-azure-sql-vm-cli-to-configure-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Настройка группы доступности AlwaysOn для SQL Server на виртуальной Машине Azure с помощью CLI виртуальной Машины SQL Azure
В этой статье описывается использование [виртуальной Машины SQL Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/) для развертывания Windows отказоустойчивого кластера (WSFC) и добавить в кластер виртуальных машин SQL Server, а также создание внутренней подсистемы балансировки нагрузки и прослушивателя для группы доступности Always On.  Фактическое развертывание группы доступности Always On по-прежнему выполняется вручную через SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Технические условия
Чтобы автоматизировать настройку группы доступности AlwaysOn с помощью интерфейса командной строки виртуальной Машины SQL Azure, требуется следующее: 
- [подписка Azure](https://azure.microsoft.com/free/);
- Группа ресурсов с контроллером домена. 
- Один или несколько присоединенных к домену [виртуальных машин в Azure выполнения SQL Server 2016 (или более поздней версии) Enterprise edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) в *одной группе доступности или в разных зонах доступности* , которые прошли [зарегистрирован в поставщике ресурсов виртуальной Машины SQL](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).  
- [Azure CLI](/cli/azure/install-azure-cli). 
- (Не используется для любой сущности) доступны два IP-адреса, один для внутренней подсистемы балансировки нагрузки и один для прослушивателя группы доступности в пределах той же подсети, что группы доступности. Если используется существующий балансировщик нагрузки, только один доступный IP-адрес необходим для прослушивателя группы доступности. 

## <a name="permissions"></a>Разрешения
Настройка группы доступности AlwaysOn с помощью интерфейса командной строки виртуальной Машины SQL Azure необходимы следующие разрешения учетной записи. 

- Существующей учетной записи домена, имеющую разрешение на «Создание объекта компьютера» в домене.  Например, учетная запись администратора домена обычно имеет соответствующие разрешения (например: account@domain.com). _Эта учетная запись также должна входить в группу локальных администраторов на каждой виртуальной машине для создания кластера._
- Учетная запись пользователя домена, который управляет служба SQL Server. 
 
## <a name="step-1---create-storage-account-as-a-cloud-witness"></a>Шаг 1 — Создание учетной записи хранения, облака-свидетеля
Кластер требуется учетная запись хранилища в качестве облако-свидетель. Можно использовать любой существующей учетной записи хранения, или можно создать новую учетную запись хранения. Если вы хотите использовать существующую учетную запись хранения, перейдите к следующему разделу. 

В следующем фрагменте кода создается учетная запись хранения: 
```azurecli
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

   >[!TIP]
   > Может появиться ошибка `az sql: 'vm' is not in the 'az sql' command group` при использовании устаревшая версия Azure CLI. Скачайте [последнюю версию Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) для устранения этой ошибки.

## <a name="step-2---define-windows-failover-cluster-metadata"></a>Шаг 2 — Определение метаданных отказоустойчивого кластера Windows
Azure CLI виртуальной Машины SQL [группы виртуальных машин sql az](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) команды, группы, который управляет метаданными службы отказоустойчивого кластера Windows (WSFC), на котором размещается группа доступности. Метаданные кластера включают домена AD, учетные записи кластера, учетные записи хранения для использования в качестве облака-свидетеля и версии SQL Server. Используйте [создать группы виртуальных машин sql az](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) для определения метаданных для WSFC, таким образом, чтобы при добавлении первую ВМ SQL Server, создается кластер, как определено. 

В следующем фрагменте кода определяет метаданные для кластера:
```azurecli
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

## <a name="step-3---add-sql-server-vms-to-cluster"></a>Шаг 3 - Добавление кластер виртуальных машин SQL Server
При добавлении первую ВМ SQL Server в кластер создается кластер. [Az sql виртуальной машины добавьте с группами](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) команда создает кластер с именем, введенным ранее, устанавливает кластерную роль на виртуальных машинах SQL Server и добавляет их в кластер. Последующее использование `az sql vm add-to-group` команда добавляет дополнительные виртуальные машины SQL Server для только что созданного кластера. 

Следующий фрагмент кода создает кластер и добавляет первую ВМ SQL Server в него: 

```azurecli
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Эта команда используется для добавления других виртуальных машин сервера SQL в кластер, только изменение `-n` параметр для имени виртуальной Машины SQL Server. 

## <a name="step-4---create-availability-group"></a>Шаг 4 — Создание группы доступности
Вручную создайте группу доступности, как обычно, с помощью [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell), или [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

  >[!IMPORTANT]
  > Сделать **не** создать прослушиватель в данный момент, так как это делается с помощью Azure CLI в следующих разделах.  

## <a name="step-5---create-internal-load-balancer"></a>Шаг 5 - Создание внутренней подсистемы балансировки нагрузки

Прослушиватель (AG) группы доступности Always On требуется внутренний балансировщик нагрузки Azure (ILB). ILB предоставляет "плавающий" IP-адрес прослушивателю группы доступности, что обеспечивает более быструю отработку отказа и восстановление подключения. Если виртуальные машины SQL Server в группе доступности входят в одну группу доступности, вы можете использовать Load Balancer ценовой категории "Базовый". Если нет, потребуется Load Balancer ценовой категории "Стандартный".  **Подсистема ILB должна находиться в той же виртуальной сети, что и экземпляры виртуальной машины SQL Server.** 

В следующем фрагменте кода создается внутренней подсистемы балансировки нагрузки:

```azurecli
# Create the Internal Load Balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

  >[!IMPORTANT]
  > Ресурс общедоступного IP-адреса для каждой виртуальной машины SQL Server должен иметь номер SKU "Стандартный", чтобы обеспечить совместимость с Load Balancer ценовой категории "Стандартный". Чтобы определить номер SKU для ресурса общедоступного IP-адреса виртуальной машины, найдите **группу ресурсов** и выберите ресурс **Общедоступный IP-адрес** для требуемой виртуальной машины SQL Server, а затем найдите для него значение параметра **Номер SKU** на панели **Обзор**.  

## <a name="step-6---create-availability-group-listener"></a>Шаг 6 - прослушивателей группы доступности AlwaysOn
После создания группы доступности были вручную, можно создать прослушиватель, используя [az sql виртуальной машины прослушивателя группы доступности-](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 


- **Идентификатор ресурса подсети** значение `/subnets/<subnetname>` добавляемый идентификатор ресурса для ресурса виртуальной сети. Чтобы определить идентификатор ресурса подсети, сделайте следующее:
   1. Перейдите к группе ресурсов в [портала Azure](https://portal.azure.com). 
   1. Выберите ресурс виртуальной сети. 
   1. Выберите **свойства** в **параметры** области. 
   1. Определить идентификатор ресурса для виртуальной сети и добавление `/subnets/<subnetname>`в конец его, чтобы создать идентификатор ресурса подсети. Например: 
        - — Идентификатор ресурса виртуальной сети: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
        - Меня зовут подсети `default`.
        - Таким образом является идентификатор ресурса подсети: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


В следующем фрагменте кода создается прослушиватель группы доступности:

```azurecli
# Create the AG listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas ex: sqlvm1 sqlvm2>
```

## <a name="modify-number-of-replicas-in-availability-group"></a>Изменение числа реплик в группе доступности
Имеется дополнительный уровень сложности при развертывании группы доступности для виртуальных машин SQL Server, размещенных в Azure, так как теперь ресурсами счет поставщика ресурсов и с помощью `virtual machine group`. Таким образом при добавлении или удалении реплик к группе доступности, есть дополнительный шаг обновления метаданных прослушиватель с информацией о виртуальных машинах SQL Server. Таким образом, при изменении количества реплик в группе доступности, необходимо также использовать [az sql виртуальной машины группа прослушивателя группы доступности обновления](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) команду, чтобы обновить прослушиватель с метаданными виртуальных машин SQL Server. 


### <a name="add-a-replica"></a>Добавление реплики

Чтобы добавить новую реплику к группе доступности, сделайте следующее:

1. Добавление виртуальной Машины SQL Server в кластер:
   ```azurecli
   # Add SQL Server VM to the Cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Позволяет добавить экземпляр SQL Server в качестве реплики группы доступности SQL Server Management Studio (SSMS).
1. Добавьте метаданные виртуальной Машины SQL Server к прослушивателю:
   ```azurecli
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Удаление реплики

Удаление реплики из группы доступности, сделайте следующее:

1. Удалите эту реплику из группы доступности, с помощью SQL Server Management Studio (SSMS). 
1. Удаление метаданных виртуальной Машины SQL Server из прослушивателя:
   ```azurecli
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Удаление виртуальной Машины SQL Server из кластера:
   ```azurecli
   # Remove SQL VM from cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-availability-group-listener"></a>Удаление прослушивателя группы доступности
Если позже вам нужно удалить прослушиватель группы доступности настроена с помощью Azure CLI, должно пройти через поставщик ресурсов виртуальной Машины SQL. Так как прослушиватель зарегистрирован в поставщике ресурсов виртуальной машины SQL, недостаточно просто удалить его с помощью SQL Server Management Studio. Он фактически будет удалена через поставщик ресурсов виртуальной Машины SQL с помощью Azure CLI. Таким образом из поставщика ресурсов виртуальной машины SQL удаляются метаданные прослушивателя группы доступности и из группы доступности физически удаляется прослушиватель. 

Следующий фрагмент кода позволяет удалить прослушиватель группы доступности SQL из поставщика ресурсов SQL и из группы доступности: 

```azurecli
# Remove the AG listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях: 

* [Общие сведения о виртуальной машине SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [Часто задаваемые вопросы о виртуальных машинах SQL Server](virtual-machines-windows-sql-server-iaas-faq.md)
* [Виртуальная машина SQL Server: заметки о выпуске](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Изменение модели лицензирования для виртуальной машины SQL Server](virtual-machines-windows-sql-ahb.md)
* [Обзор групп доступности Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Настройка экземпляра сервера для групп доступности Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Администрирование группы доступности &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Отслеживание групп доступности &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Общие сведения об инструкциях Transact-SQL для групп доступности Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Обзор командлетов PowerShell для групп доступности Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
