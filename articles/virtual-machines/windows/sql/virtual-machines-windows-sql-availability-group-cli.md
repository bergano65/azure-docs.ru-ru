---
title: Настройка группы доступности (Azure CLI)
description: Используйте Azure CLI для создания кластера Сбой Windows, слушателя группы доступности и баланса внутренней нагрузки на VM сервера S'L в Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022368"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Используйте Azure CLI для настройки группы всегда на доступности для сервера S'L на Azure VM
В этой статье описывается, как использовать [Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/) для развертывания кластера совмещения Windows, добавления в кластер VMs-сервера, а также создания баланса внутренней нагрузки и слушателя для группы всегда на наличие. Развертывание группы всегда на доступность по-прежнему осуществляется вручную через студию управления серверами S'L (SSMS). 

## <a name="prerequisites"></a>Предварительные требования
Чтобы автоматизировать настройку группы всегда на доступность с помощью Azure CLI, необходимо иметь следующие предпосылки: 
- [Подписка Azure](https://azure.microsoft.com/free/).
- Группа ресурсов с контроллером домена. 
- Один или несколько конментов, объединенных [VMs в Azure под управлением S'L Server 2016 (или позже) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) в том *же наборе доступности или в различных зонах доступности,* которые были [зарегистрированы с поставщиком ресурсов S'L VM.](virtual-machines-windows-sql-register-with-resource-provider.md)  
- [Лазурный CLI](/cli/azure/install-azure-cli). 
- Два доступных (не используемых какой-либо организацией) IP-адреса. Один из них предназначен для внутреннего баланса нагрузки. Другой - для слушателя группы доступности в той же подсети, что и группа доступности. Если вы используете существующий балансер нагрузки, вам нужен только один доступный IP-адрес для слушателя группы доступности. 

## <a name="permissions"></a>Разрешения
Для настройки группы всегда на доступности с помощью Azure CLI требуется следующее разрешение учетной записи: 

- Существующая учетная запись пользователя домена, которая имеет разрешение **на создание компьютерного объекта** в домене. Например, учетная запись domain Admin обычно account@domain.comимеет достаточное разрешение (например: ). _Эта учетная запись также должна входить в группу локальных администраторов на каждой виртуальной машине для создания кластера._
- Учетная запись пользователя домена, которая управляет службой сервера S'L. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>Шаг 1: Создание учетной записи хранилища в качестве свидетеля облака
Кластеру нужна учетная запись хранения, чтобы выступать в качестве свидетеля облака. Вы можете использовать любую существующую учетную запись хранения или создать новую учетную запись хранения. Если вы хотите использовать существующую учетную запись хранения, перейдите к следующему разделу. 

Следующий фрагмент кода создает учетную запись хранения: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Ошибка может быть `az sql: 'vm' is not in the 'az sql' command group` проислется, если используете устаревшую версию CLI Azure. Загрузите [последнюю версию Azure CLI,](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) чтобы пройти эту ошибку.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>Шаг 2: Определить метаданные кластера сбоя Windows
Группа командных данных Azure CLI [az sql vm](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) управляет метаданными службы Windows Server Failover Cluster (WSFC), в котором размещается группа доступности. Метаданные кластера включают в себя домен Active Directory, учетные записи кластеров, учетные записи хранения, которые будут использоваться в качестве свидетеля облака, и версию S'L Server. Используйте [группу az sql vm](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) для определения метаданных для WSFC, чтобы при добавлении первого сервера S'L VM кластер создавался по мере определенного. 

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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>Шаг 3: Добавление VMs-сервера S'L в кластер
Добавление первого VM сервера S'L в кластер создает кластер. Команда [надстройки az sql vm](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) создает кластер с ранее приведенным названием, устанавливает роль кластера на vMs-серверах S'L Server и добавляет их в кластер. Последующее использование `az sql vm add-to-group` команды добавляет больше vMs сервера S'L server в недавно созданный кластер. 

Следующий фрагмент кода создает кластер и добавляет к нему первый VM сервера S'L: 

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
Используйте эту команду, чтобы добавить в кластер все другие вм: Серверный сервер. Изменяйте `-n` только параметр для имени S'L Server VM. 

## <a name="step-4-create-the-availability-group"></a>Шаг 4: Создание группы доступности
Вручную создайте группу доступности, как обычно, с помощью [студии управления серверами S'L,](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio) [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)или [Transact-S'L.](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql) 

>[!IMPORTANT]
> Не *not* создавайте слушателя в настоящее время, потому что это делается через Azure CLI в следующих разделах.  

## <a name="step-5-create-the-internal-load-balancer"></a>Шаг 5: Создание внутреннего баланса нагрузки

Слушателю группы всегда на наличие требуется внутренний экземпляр баланса загрузки Azure. Внутренний балансизатор нагрузки обеспечивает "плавающий" IP-адрес для слушателя группы доступности, что позволяет быстрее выводить из строя и воссоединения. Если ВМ сервера S'L в группе доступности являются частью одного и того же набора доступности, можно использовать балансера basic load. В противном случае необходимо использовать балансизатор стандартной нагрузки.  

> [!NOTE]
> Внутренний балансосиватор нагрузки должен находиться в той же виртуальной сети, что и экземпляры S'L Server VM. 

Следующий фрагмент кода создает внутренний балансер нагрузки:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> Общедоступный ip-ресурс для каждого S'L Server VM должен иметь стандартный SKU, чтобы быть совместимым со балансиватором стандартной нагрузки. Чтобы определить SKU общедоступного IP-ресурса вашего VM, перейдите в **Группу ресурсов,** выберите ресурс **общественного IP-адреса** для желаемого S'L Server VM и найдите значение под **SKU** в панели **Обзора.**  

## <a name="step-6-create-the-availability-group-listener"></a>Шаг 6: Создание группы слушателя доступности
После ручного создания группы доступности можно создать слушателя с помощью [az sql vm ag-listener.](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create) 

*Идентификатор ресурсов подсети* — это значение, приложено `/subnets/<subnetname>` к идентификатору ресурса виртуального сетевого ресурса. Для определения идентификатора ресурсов подсети:
   1. Перейдите к группе ресурсов на [портале Azure](https://portal.azure.com). 
   1. Выберите виртуальный сетевой ресурс. 
   1. Выберите **свойства** в панели **настроек.** 
   1. Определите идентификатор ресурса `/subnets/<subnetname>` для виртуальной сети и притязание к концу для создания идентификатора ресурсов подсети. Пример:
      - Идентификатор виртуального сетевого ресурса:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Ваше имя подсети:`default`
      - Таким образом, идентификатор ресурсов подсети:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Следующий фрагмент кода создает слушателя группы доступности:

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
При развертывании группы доступности для VMs-серверов, размещенных в Azure, возникает дополнительный уровень сложности. Поставщик ресурсов и виртуальная группа машин теперь управляют ресурсами. Таким образом, при добавлении или удалении реплик в группе доступности происходит дополнительный этап обновления метаданных слушателя с информацией о ВМ сервера S'L. При изменении количества реплик в группе доступности необходимо также использовать команду [обновления группы az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) для обновления слушателя с помощью метаданных VMs-сервера S'L. 


### <a name="add-a-replica"></a>Добавление реплики

Чтобы добавить новую реплику в группу доступности:

1. Добавьте vM сервера S'L к кластеру:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Для добавления экземпляра сервера в качестве реплики в группе доступности — «Студия управления серверами» —L Server Studio.
1. Добавьте метаданные S'L Server VM слушателю:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Удаление реплики

Чтобы удалить реплику из группы доступности:

1. Удалите реплику из группы доступности, используя студию управления серверами S'L. 
1. Удалите метаданные сервера VM от слушателя:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Удалите VM сервера S'L из кластера:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>Удалить слушателя группы доступности
Если позже необходимо удалить слушатель группы доступности, настроенный с помощью Azure CLI, необходимо пройти через поставщика ресурсов S'L VM. Поскольку слушатель зарегистрирован через поставщика ресурсов S'L VM, простого удаляния его через студию управления серверами S'L недостаточно. 

Наилучшим методом является удаление его через поставщика ресурсов S'L VM, используя следующий фрагмент кода в Azure CLI. Это удаляет метаданные группы слушателей доступности из поставщика ресурсов S'L VM. Он также физически удаляет слушателя из группы доступности. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях: 

* [Обзор VMs-серверов S'L](virtual-machines-windows-sql-server-iaas-overview.md)
* [Часто задаваемые вопросы для VMs-серверов S'L](virtual-machines-windows-sql-server-iaas-faq.md)
* [Выпуск примечаний для VMs-сервера S'L](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Изменение модели лицензирования для виртуальной машины SQL Server](virtual-machines-windows-sql-ahb.md)
* [Обзор групп доступности всегда на &#40;&#41;сервера S'L](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Конфигурация экземпляра сервера для групп доступности Always On &#40;S'L Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Администрирование группы доступности &#40;&#41;сервера S'L](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Отслеживание групп доступности (SQL Server)](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Обзор заявлений о Трансакт-СЗЛ для групп доступности всегда на &#40;s-L Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Обзор смдлетов PowerShell для всегда на группах доступности &#40;s'L Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
