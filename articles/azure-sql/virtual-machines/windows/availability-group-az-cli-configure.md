---
title: Настройка группы доступности с помощью Azure CLI
description: Создайте отказоустойчивый кластер Windows, прослушиватель группы доступности и внутреннюю подсистему балансировки нагрузки на виртуальной машине SQL Server в Azure с помощью Azure CLI.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3366438012ecc3395e7f4ae3774316ef1ddcd3b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669347"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-azure-vm"></a>Настройка группы доступности Always On для SQL Server на виртуальной машине Azure с помощью Azure CLI
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

В этой статье описывается, как с помощью [Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/) развернуть отказоустойчивый кластер Windows, добавить виртуальные машины SQL Server в кластер, а также создать внутреннюю подсистему балансировки нагрузки и прослушиватель для группы доступности Always On. Развертывание группы доступности Always On по-прежнему выполняется вручную с помощью SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Предварительные требования
Чтобы автоматизировать настройку группы доступности Always On с помощью Azure CLI, необходимо следующее: 
- [Подписка Azure](https://azure.microsoft.com/free/).
- Группа ресурсов с контроллером домена. 
- Одна или несколько присоединенных к домену [виртуальных машин Azure под управлением SQL Server Enterprise Edition 2016 (или более поздней версии)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision), размещенных в *той же группе доступности или в разных зонах доступности*, которые [зарегистрированы в поставщике ресурсов виртуальной машины SQL](sql-vm-resource-provider-register.md).  
- [Интерфейс командной строки Azure](/cli/azure/install-azure-cli). 
- Два доступных (неиспользуемых сущностью) IP-адреса. Один предназначен для внутренней подсистемы балансировки нагрузки. Второй — для прослушивателя группы доступности в той же подсети, что и группа доступности. Если вы используете существующую подсистему балансировки нагрузки, для прослушивателя группы доступности требуется предоставить только один доступный IP-адрес. 

## <a name="permissions"></a>Разрешения
Для настройки группы доступности Always On с помощью Azure CLI необходимы разрешения следующих учетных записей: 

- Существующая учетная запись пользователя домена с разрешением на **создание объекта компьютера** в домене. Например, учетная запись администратора домена обычно имеет соответствующие разрешения (например: account@domain.com). _Эта учетная запись также должна входить в группу локальных администраторов на каждой виртуальной машине для создания кластера._
- Учетная запись пользователя домена, которая используется для управления SQL Server. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>Шаг 1. Создание учетной записи хранения как облака-свидетеля
Для кластера требуется учетная запись хранения, которая будет использоваться в качестве облака-свидетеля. Вы можете использовать существующую учетную запись хранения или создать ее. Если вы хотите использовать существующую учетную запись хранения, перейдите к следующему разделу. 

Следующий фрагмент кода создает учетную запись хранения: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Если вы используете устаревшую версию Azure CLI, может появиться сообщение об ошибке `az sql: 'vm' is not in the 'az sql' command group`. Скачайте [последнюю версию Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest), чтобы устранить эту ошибку.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>Шаг 2. Определение метаданных отказоустойчивого кластера Windows
Группа команд [az sql vm group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) Azure CLI управляет метаданными службы отказоустойчивого кластера Windows Server (WSFC), в которой размещается группа доступности. К метаданным кластера относятся домен Active Directory, учетные записи кластера, учетные записи хранения, которые используются в роли облака-свидетеля, и версия SQL Server. Используйте [az sql vm group create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) для определения метаданных для WSFC, чтобы при добавлении первой виртуальной машины SQL Server был создан кластер в соответствии с определением. 

Следующий фрагмент кода определяет метаданные для кластера:
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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>Шаг 3. Добавление виртуальных машин SQL Server в кластер
При добавлении первой виртуальной машины SQL Server в кластер создается кластер. Вы можете создать кластер с указанным ранее именем, установить для него роль на виртуальных машинах SQL Server, а также добавить их в кластер с помощью команды [az sql vm add-to-group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group). Выполните команду `az sql vm add-to-group` еще раз, чтобы добавить в созданный кластер дополнительные виртуальные машины SQL Server. 

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
Используйте эту команду, чтобы добавить в кластер любые другие виртуальные машины SQL Server. Для имени виртуальной машины SQL Server измените только параметр `-n`. 

## <a name="step-4-create-the-availability-group"></a>Шаг 4. Создание группы доступности
Создайте группу доступности вручную, как обычно, с помощью [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell) или [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Не* создавайте прослушиватель на этом шаге. Это действие будет выполнено с помощью Azure CLI в следующих разделах.  

## <a name="step-5-create-the-internal-load-balancer"></a>Шаг 5. Создание внутренней подсистемы балансировки нагрузки

Прослушивателю группы доступности Always On требуется внутренний экземпляр Azure Load Balancer. Внутренняя подсистема балансировки нагрузки предоставляет "плавающий" IP-адрес прослушивателю группы доступности, что обеспечивает более быструю отработку отказа и восстановление подключения. Если виртуальные машины SQL Server в группе доступности входят в одну группу доступности, вы можете использовать подсистему балансировки нагрузки ценовой категории "Базовый". В противном случае используйте подсистему балансировки нагрузки ценовой категории "Стандартный".  

> [!NOTE]
> Внутренняя подсистема балансировки нагрузки должна находиться в той же виртуальной сети, что и экземпляры виртуальных машин SQL Server. 

Следующий фрагмент кода создает внутреннюю подсистему балансировки нагрузки:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> Ресурс общедоступного IP-адреса для каждой виртуальной машины SQL Server должен иметь номер SKU "Стандартный", чтобы обеспечить совместимость с подсистемой балансировки нагрузки ценовой категории "Стандартный". Чтобы определить номер SKU для ресурса общедоступного IP-адреса виртуальной машины, найдите **группу ресурсов** и выберите ресурс **Общедоступный IP-адрес** для требуемой виртуальной машины SQL Server, а затем найдите для него значение параметра **Номер SKU** на панели **Обзор**.  

## <a name="step-6-create-the-availability-group-listener"></a>Шаг 6. Создание прослушивателя группы доступности
После создания группы доступности вручную можно создать прослушиватель с помощью команды [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 

*Идентификатор ресурса подсети* — это значение `/subnets/<subnetname>`, которое добавляется к идентификатору ресурса виртуальной сети. Чтобы определить идентификатор ресурса подсети, выполните следующие действия:
   1. Перейдите к группе ресурсов на [портале Azure](https://portal.azure.com). 
   1. Выберите ресурс виртуальной сети. 
   1. Выберите **Свойства** в области **Параметры**. 
   1. Определите идентификатор ресурса для виртуальной сети и добавьте `/subnets/<subnetname>` к нему в конце, чтобы создать идентификатор ресурса подсети. Пример:
      - Ваш идентификатор ресурса виртуальной сети — `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`.
      - Ваше имя подсети — `default`.
      - Таким образом, ваш идентификатор ресурса подсети — `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`.


Следующий фрагмент кода создает прослушиватель группы доступности:

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
Существует дополнительный уровень сложности при развертывании группы доступности в виртуальных машинах SQL Server, размещенных в Azure. Поставщик ресурсов и группа виртуальных машин теперь управляют ресурсами. Таким образом, при добавлении или удалении реплик в группе доступности выполняется дополнительная задача добавления сведений о виртуальных машинах SQL Server в метаданные прослушивателя. При изменении числа реплик в группе доступности необходимо также использовать команду [az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update), чтобы добавить в прослушиватель метаданные виртуальных машин SQL Server. 


### <a name="add-a-replica"></a>Добавление реплики

Чтобы добавить новую реплику в группу доступности, выполните следующие действия:

1. Добавьте виртуальную машину SQL Server в кластер, выполнив следующую команду:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Используйте SQL Server Management Studio, чтобы добавить экземпляр SQL Server в качестве реплики в группу доступности.
1. Добавьте метаданные виртуальной машины SQL Server в прослушиватель, выполнив следующую команду:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Удаление реплики

Чтобы удалить реплику из группы доступности, выполните следующие действия:

1. Удалите реплику из группы доступности с помощью SQL Server Management Studio. 
1. Удалите метаданные виртуальной машины SQL Server из прослушивателя, выполнив следующую команду:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Удалите виртуальную машину SQL Server из кластера, выполнив следующую команду:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>Удаление прослушивателя группы доступности
Если вам потребуется удалить прослушиватель группы доступности, настроенный с помощью Azure CLI, это нужно делать через поставщик ресурсов виртуальной машины SQL. Так как прослушиватель зарегистрирован в поставщике ресурсов виртуальной машины SQL, недостаточно просто удалить его с помощью SQL Server Management Studio. 

Лучший способ — удалить его с помощью поставщика ресурсов виртуальной машины SQL, используя приведенный ниже фрагмент кода в Azure CLI. Таким образом из поставщика ресурсов виртуальной машины SQL удаляются метаданные прослушивателя группы доступности, а из группы доступности физически удаляется прослушиватель. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях: 

* [Общие сведения о виртуальных машинах SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Часто задаваемые вопросы о виртуальных машинах SQL Server](frequently-asked-questions-faq.md)
* [Заметки о виртуальных машинах SQL Server](../../database/doc-changes-updates-release-notes.md)
* [Изменение модели лицензирования для виртуальной машины SQL Server](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Общие сведения о группах доступности Always On (&#40;SQL Server&#41;)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Настройка экземпляра сервера для групп доступности Always On (&#40;SQL Server&#41;)](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Администрирование группы доступности (&#40;SQL Server&#41;)](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Отслеживание групп доступности (SQL Server)](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Общие сведения об инструкциях Transact-SQL для групп доступности Always On (&#40;SQL Server&#41;)](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Обзор командлетов PowerShell для групп доступности Always On (&#40;SQL Server&#41;)](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
