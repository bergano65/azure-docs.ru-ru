---
title: Исключить VM-диски Azure из репликации с помощью восстановления сайта Azure и Azure PowerShell
description: Узнайте, как исключить диски виртуальных машин Azure во время восстановления сайта Azure с помощью Azure PowerShell.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: 7355233bb7241571e3f3820aafac6952af245654
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973682"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Исключить диски из репликации VMs Azure

В этой статье описывается, как исключить диски при репликации VMs Azure. Можно исключить диски для оптимизации потребляемой пропускной способности репликации или целевых ресурсов, используемых этими дисками. В настоящее время эта возможность доступна только через Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

Перед началом:

- Убедитесь, что вы понимаете [архитектуру и компоненты аварийного восстановления.](azure-to-azure-architecture.md)
- Просмотрите [требования к поддержке](azure-to-azure-support-matrix.md) всех компонентов.
- Убедитесь, что у вас есть модуль AzureRm PowerShell "Az". Чтобы установить или обновить PowerShell, смотрите [модуль Azure PowerShell.](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Убедитесь, что вы создали хранилище служб восстановления и защитили виртуальные машины по крайней мере один раз. Если вы еще не сделали этого, следите за процессом [настройки аварийного восстановления для виртуальных машин Azure с помощью Azure PowerShell.](azure-to-azure-powershell.md)
- Если вы ищете информацию о добавлении дисков в Azure VM, включенный для репликации, [просмотрите эту статью.](azure-to-azure-enable-replication-added-disk.md)

## <a name="why-exclude-disks-from-replication"></a>Зачем исключать диски из репликации
Возможно, потребуется исключить диски из репликации, поскольку:

- Ваша виртуальная машина достигла [пределов восстановления сайта Azure для воспроизведения скорости изменения данных.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)

- Данные, вспененные на исключенном диске, не важны или не должны быть воспроизведены.

- Вы хотите сохранить ресурсы хранения и сети, не реплицируя данные.

## <a name="how-to-exclude-disks-from-replication"></a>Как исключить диски из репликации

В нашем примере мы реплицируем виртуальную машину с одной ОС и тремя дисками данных, которые в восточном регионе США в регионе Западного США 2. Название виртуальной машины *AzureDemoVM*. Мы исключаем диск 1 и храним диски 2 и 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Получить подробную информацию о виртуальных машинах для репликации

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

Получите подробную информацию о дисках виртуальной машины. Эта информация будет использоваться позже, когда вы начнете репликацию VM.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Повторите виртуальную машину Azure

В следующем примере мы предполагаем, что у вас уже есть учетная запись хранения кэша, политика репликации и отображение. Если у вас нет этих вещей, следите за процессом [настройки аварийного восстановления для виртуальных машин Azure с помощью Azure PowerShell.](azure-to-azure-powershell.md)

Воспроизвести виртуальную машину Azure с *управляемыми дисками.*

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

При успешном запуске операции репликации данные VM реплицируются в область восстановления.

Вы можете перейти на портал Azure и увидеть реплицированные вм: под "реплицированными элементами".

Процесс репликации начинается с посева копии репликирующих дисков виртуальной машины в зоне восстановления. Эта фаза называется фазой первоначальной репликации.

После завершения первоначальной репликации репликация переходит к фазе синхронизации дифференциала. На этом этапе виртуальная машина защищена. Выберите защищенную виртуальную машину, чтобы увидеть, если какие-либо диски исключены.

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о [запуске теста failover](site-recovery-test-failover-to-azure.md).
