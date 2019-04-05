---
title: Azure Site Recovery. Исключение диска во время репликации виртуальных машин Azure с помощью Azure PowerShell | Документация Майкрософт
description: Сведения об исключении диска для виртуальных машин с Azure Site Recovery Azure с помощью Azure PowerShell.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 1c278d810df7e5ba8701529a59987c9bb16fa40c
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044131"
---
# <a name="exclude-disks-from-replication-of-azure-vms-to-azure-using-azure-powershell"></a>Исключение дисков из репликации виртуальных машин в Azure с помощью Azure PowerShell

В этой статье описано, как исключать диски при репликации виртуальных машин Azure. Благодаря этому можно оптимизировать использование пропускной способности при репликации или же ресурсы целевой стороны, используемые такими дисками. Сейчас эта возможность предоставляется только с помощью Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Технические условия

Перед началом работы:

- Вам должны быть понятны [архитектура и компоненты сценария](azure-to-azure-architecture.md).
- [Ознакомьтесь](azure-to-azure-support-matrix.md) с требованиями поддержки для всех компонентов.
- Среда Azure PowerShell `Az` модуля. Если вам необходимо установить или обновить Azure PowerShell, ознакомьтесь с этим [руководством по установке и настройке Azure PowerShell](/powershell/azure/install-az-ps).
- Ваше хранилище Служб восстановления уже создано и защита виртуальных машин выполнялась по крайней мере один раз. Если не выполните его с помощью документации, упомянутой [здесь](azure-to-azure-powershell.md).

## <a name="why-exclude-disks-from-replication"></a>Зачем исключать диски из репликации
Исключение дисков из репликации часто бывает необходимо:

- Виртуальная машина достигла [пределов Azure Site Recovery для скорости репликации измененных данных](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)

- Если данные, находящиеся на исключенных дисках, не важны или их репликация не требуется.

- Ресурсы хранилища и сети можно сохранять, не реплицируя данные этого потока.


## <a name="how-to-exclude-disks-from-replication"></a>Как исключать диски из репликации

В примере для этой статьи виртуальная машина, которая имеет 1 диск ОС и 3 диска из региона "Восточная часть США" будет реплицирована в регион "Западная часть США 2". Виртуальная машина в этом примере имеет имя AzureDemoVM. Мы исключим диск 1, а диск 2 и 3 сохраним.

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Получение сведений о реплицируемой виртуальной машине

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```


Получите сведения о дисках виртуальной машины. Сведения о дисках потребуются позже для запуска репликации виртуальной машины.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-azure-virtual-machine"></a>Репликация виртуальной машины Azure

В приведенном ниже примере мы предположили, что у вас уже есть учетная запись хранения для кэширования, политика репликации и сопоставления. Если нет, выполните это с помощью документации, описанной [здесь](azure-to-azure-powershell.md) 


Выполните репликацию виртуальной машины Azure с помощью **управляемых дисков**.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded so we will provide it during the time of replication 

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Когда начальная репликация успешно завершится, все данные виртуальной машины будут реплицированы в регион восстановления.

Вы можете перейти на портал Azure и в реплицированных элементах отобразится репликация виртуальных машин.
Процесс репликации начинается с того, что в регионе восстановления создаются копии реплицируемых дисков виртуальной машины. Этот этап называется начальной репликацией.

После завершения начальной репликации начинается этап разностной синхронизации. На этом этапе виртуальная машина защищена. Чтобы увидеть исключен диск или нет, щелкните защищенную виртуальную машину, а затем диски.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о выполнении тестовой отработки отказа см. в [этой статье](site-recovery-test-failover-to-azure.md).
