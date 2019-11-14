---
title: Настройка группы доступности (Azure CLI)
description: Используйте Azure CLI для создания отказоустойчивого кластера Windows, прослушивателя группы доступности и внутренней подсистемы балансировки нагрузки на виртуальной машине SQL Server в Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: a6600af353daf2bfa7b49196f48ba5b60e6c45fb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022368"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Настройка группы доступности Always On для SQL Server на виртуальной машине Azure с помощью Azure CLI
В этой статье описывается, как использовать [Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/) для развертывания отказоустойчивого кластера Windows, добавления SQL Server виртуальных машин в кластер и создания внутренней подсистемы балансировки нагрузки и прослушивателя для Always on группы доступности. Развертывание группы доступности Always On по-прежнему выполняется вручную с помощью SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>предварительным требованиям
Чтобы автоматизировать настройку Always On группы доступности с помощью Azure CLI, необходимо выполнить следующие предварительные требования. 
- [Подписка Azure](https://azure.microsoft.com/free/).
- Группа ресурсов с контроллером домена. 
- Одна или несколько виртуальных машин, присоединенных к домену, [в Azure под управлением SQL Server 2016 (или более поздней версии) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) в *одной группе доступности или разных зонах доступности* , [зарегистрированных в поставщике ресурсов виртуальной машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md).  
- [Интерфейс командной строки Azure](/cli/azure/install-azure-cli). 
- Два доступных (не используемые ни одними) IP-адреса. Один предназначен для внутреннего балансировщика нагрузки. Второй — для прослушивателя группы доступности в той же подсети, что и группа доступности. Если вы используете существующую подсистему балансировки нагрузки, для прослушивателя группы доступности требуется только один доступный IP-адрес. 

## <a name="permissions"></a>Разрешения
Для настройки группы доступности Always On с помощью Azure CLI необходимы следующие разрешения учетной записи: 

- Существующая учетная запись пользователя домена, имеющая разрешение на **Создание объекта Computer** в домене. Например, учетная запись администратора домена обычно имеет достаточные разрешения (например, account@domain.com). _Эта учетная запись также должна входить в группу локальных администраторов на каждой виртуальной машине для создания кластера._
- Учетная запись пользователя домена, управляющая службой SQL Server. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>Шаг 1. Создание учетной записи хранения в качестве облачного следящего сервера
Кластеру требуется учетная запись хранения, которая будет использоваться в качестве облачного следящего сервера. Вы можете использовать любую существующую учетную запись хранения или создать новую. Если вы хотите использовать существующую учетную запись хранения, перейдите к следующему разделу. 

В следующем фрагменте кода создается учетная запись хранения: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Если вы используете устаревшую версию Azure CLI, может появиться сообщение об ошибке `az sql: 'vm' is not in the 'az sql' command group`. Скачайте [последнюю версию Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) , чтобы устранить эту ошибку.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>Шаг 2. Определение метаданных отказоустойчивого кластера Windows
Группа команд Azure CLI [AZ SQL VM Group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) управляет метаданными службы отказоустойчивого кластера Windows Server (WSFC), в которой размещается группа доступности. Метаданные кластера включают домен Active Directory, учетные записи кластера, учетные записи хранения, которые будут использоваться в качестве облака-свидетеля, и SQL Server версии. Для определения метаданных для WSFC, чтобы при добавлении первой SQL Server виртуальной машины был создан кластер, используйте команду [AZ SQL VM Group Create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) . 

В следующем фрагменте кода определяются метаданные для кластера.
```azurecli-interactive
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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>Шаг 3. Добавление SQL Server виртуальных машин в кластер
Добавление первой SQL Server виртуальной машины в кластер создает кластер. Команда [AZ SQL VM Add-to-Group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) создает кластер с указанным ранее именем, устанавливает роль кластера на SQL Server виртуальных машинах и добавляет их в кластер. Последующие применения команды `az sql vm add-to-group` добавляют дополнительные SQL Server виртуальные машины в созданный кластер. 

Следующий фрагмент кода создает кластер и добавляет в него первую виртуальную машину SQL Server: 

```azurecli-interactive
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
Используйте эту команду, чтобы добавить в кластер любые другие SQL Server виртуальные машины. Измените только параметр `-n` для имени виртуальной машины SQL Server. 

## <a name="step-4-create-the-availability-group"></a>Шаг 4. Создание группы доступности
Вручную создайте группу доступности, как обычно, с помощью [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)или [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Не* создавайте прослушиватель в данный момент, так как это выполняется с помощью Azure CLI в следующих разделах.  

## <a name="step-5-create-the-internal-load-balancer"></a>Шаг 5. создание внутренней подсистемы балансировки нагрузки

Прослушивателю группы доступности Always On требуется внутренний экземпляр Azure Load Balancer. Внутренний балансировщик нагрузки предоставляет "плавающий" IP-адрес для прослушивателя группы доступности, который обеспечивает более быструю отработку отказа и повторное подключение. Если SQL Server виртуальные машины в группе доступности входят в одну группу доступности, можно использовать базовую подсистему балансировки нагрузки. В противном случае необходимо использовать стандартный балансировщик нагрузки.  

> [!NOTE]
> Внутренняя подсистема балансировки нагрузки должна находиться в той же виртуальной сети, что и SQL Server экземпляры виртуальных машин. 

В следующем фрагменте кода создается внутренняя подсистема балансировки нагрузки:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> Ресурс общедоступного IP-адреса для каждой SQL Server виртуальной машины должен иметь стандартный SKU, совместимый с подсистемой балансировки нагрузки уровня "Стандартный". Чтобы определить номер SKU ресурса общедоступного IP-адреса виртуальной машины, перейдите в раздел **Группа ресурсов**, выберите ресурс **общедоступного IP-адрес** для нужной виртуальной машины SQL Server и найдите значение в поле **SKU** в области **Обзор** .  

## <a name="step-6-create-the-availability-group-listener"></a>Шаг 6. Создание прослушивателя группы доступности
После создания группы доступности вручную можно создать прослушиватель с помощью команды [AZ SQL VM AG-Listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 

*Идентификатор ресурса подсети* — это значение `/subnets/<subnetname>` добавляется к идентификатору ресурса виртуальной сети. Чтобы задать идентификатор ресурса подсети, выполните следующие действия.
   1. Перейдите к группе ресурсов в [портал Azure](https://portal.azure.com). 
   1. Выберите ресурс виртуальной сети. 
   1. На панели **Параметры** выберите **Свойства** . 
   1. Найдите идентификатор ресурса для виртуальной сети и добавьте `/subnets/<subnetname>` к его концу, чтобы создать идентификатор ресурса подсети. Например,
      - Идентификатор ресурса виртуальной сети: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Имя вашей подсети: `default`
      - Таким образом, идентификатор ресурса подсети: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


В следующем фрагменте кода создается прослушиватель группы доступности:

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>Изменение числа реплик в группе доступности
Существует дополнительный уровень сложности при развертывании группы доступности для SQL Server виртуальных машин, размещенных в Azure. Поставщик ресурсов и группа виртуальных машин теперь управляют ресурсами. Таким образом, при добавлении или удалении реплик в группе доступности существует дополнительный шаг обновления метаданных прослушивателя сведениями о SQL Server виртуальных машинах. При изменении числа реплик в группе доступности необходимо также использовать команду [AZ SQL VM Group AG-Listener Update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) , чтобы обновить прослушиватель метаданными, используя метаданные SQL Server виртуальных машин. 


### <a name="add-a-replica"></a>Добавление реплики

Чтобы добавить новую реплику в группу доступности, выполните следующие действия.

1. Добавьте SQL Server виртуальную машину в кластер:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Используйте SQL Server Management Studio, чтобы добавить экземпляр SQL Server в качестве реплики в группу доступности.
1. Добавьте SQL Server метаданные виртуальной машины в прослушиватель:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Удаление реплики

Чтобы удалить реплику из группы доступности, выполните следующие действия.

1. Удалите реплику из группы доступности с помощью SQL Server Management Studio. 
1. Удалите SQL Server метаданные виртуальной машины из прослушивателя:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Удалите SQL Serverную виртуальную машину из кластера:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>Удаление прослушивателя группы доступности
Если позже потребуется удалить прослушиватель группы доступности, настроенный с Azure CLI, необходимо пройти по поставщику ресурсов виртуальной машины SQL. Так как прослушиватель зарегистрирован с помощью поставщика ресурсов виртуальной машины SQL, просто его удаление с помощью SQL Server Management Studio недостаточно. 

Лучший способ — удалить его с помощью поставщика ресурсов виртуальной машины SQL, используя следующий фрагмент кода в Azure CLI. При этом удаляются метаданные прослушивателя группы доступности из поставщика ресурсов виртуальной машины SQL. Он также физически удаляет прослушиватель из группы доступности. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в следующих статьях: 

* [Обзор SQL Server виртуальных машин](virtual-machines-windows-sql-server-iaas-overview.md)
* [Часто задаваемые вопросы о SQL Server виртуальных машинах](virtual-machines-windows-sql-server-iaas-faq.md)
* [Заметки о выпуске для SQL Server виртуальных машин](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Изменение модели лицензирования для виртуальной машины SQL Server](virtual-machines-windows-sql-ahb.md)
* [Общие сведения о &#40;группы доступности Always on SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Конфигурация экземпляра сервера для группы доступности Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Администрирование группы &#40;доступности SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Наблюдение за группами &#40;доступности SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Общие сведения о инструкциях Transact-SQL &#40;для группы доступности Always on SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Общие сведения о командлетах PowerShell &#40;для группы доступности Always on SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
