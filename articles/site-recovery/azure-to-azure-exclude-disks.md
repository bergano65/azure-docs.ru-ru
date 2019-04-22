---
title: Azure Site Recovery - исключения дисков во время репликации виртуальных машин Azure с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как исключать диски виртуальных машин Azure во время восстановления сайта Azure с помощью Azure PowerShell.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 54a32d7f7aa4bcab73f5828da3e7eba9d25276be
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678281"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Исключение дисков из репликации виртуальных машин Azure PowerShell

В этой статье описывается, как исключать диски при репликации виртуальных машин Azure. Можно исключить диски для оптимизации пропускной способности при репликации потребляемого или ресурсов целевой стороны, использующие эти диски. В настоящее время эта возможность доступна только через Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Технические условия

Перед началом работы:

- Убедитесь, что вы понимаете [аварийного восстановления архитектуре и компонентах](azure-to-azure-architecture.md).
- [Ознакомьтесь](azure-to-azure-support-matrix.md) с требованиями поддержки для всех компонентов.
- Убедитесь, что у вас есть AzureRm PowerShell «Az» модуля. Чтобы установить или обновить PowerShell, см. в разделе [установить модуль Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Убедитесь, что вы создали хранилище служб восстановления и защищенных виртуальных машин по крайней мере один раз. Если вы еще не сделано, выполните этот процесс для [Настройка аварийного восстановления для виртуальных машин Azure с помощью Azure PowerShell](azure-to-azure-powershell.md).

## <a name="why-exclude-disks-from-replication"></a>Зачем исключать диски из репликации
Может потребоваться исключить диски из репликации, так как:

- Виртуальный компьютер был достигнут [ограничения Azure Site Recovery для репликации данных изменить тарифы](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix).

- Данные, находящиеся на исключенных дисках, не имеет значения или не нужно реплицировать.

- Вы хотите сохранить ресурсы хранилища и сети, не реплицируя данные.

## <a name="how-to-exclude-disks-from-replication"></a>Как исключать диски из репликации

В нашем примере мы реплицировать виртуальную машину с одной ОС и трех дисков данных, что в регионе "восточная часть США" в регион Западная часть США 2. Имя виртуальной машины — *AzureDemoVM*. Мы исключить диск 1 и сохраните диски 2 и 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Получение сведений о виртуальных машин для репликации

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

Получите подробные сведения о дисках виртуальной машины. Эта информация будет использоваться позже при запуске репликации виртуальной машины.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Репликация виртуальной машины Azure

В следующем примере предполагается наличие учетной записи хранения кэша, политику репликации и сопоставления. Если у вас нет этих вещей, выполните этот процесс для [Настройка аварийного восстановления для виртуальных машин Azure с помощью Azure PowerShell](azure-to-azure-powershell.md).

Репликация виртуальной машины Azure с помощью *управляемые диски*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication. 

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


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

После успешного завершения операции начала репликации данных виртуальной Машины реплицируется в регион восстановления.

Перейдите на портал Azure и см. в разделе реплицированные виртуальные машины в разделе «реплицированные элементы».

Процесс репликации начинается с заполнения копию дисков репликации виртуальной машины в регионе восстановления. Этот этап называется фазой начальной репликации.

После завершения начальной репликации, репликация переходит к фазы синхронизации разностной резервной копии. На этом этапе виртуальная машина защищена. Выберите защищенную виртуальную машину, чтобы увидеть, если все диски, исключаются.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [тестовой отработке отказа](site-recovery-test-failover-to-azure.md).
