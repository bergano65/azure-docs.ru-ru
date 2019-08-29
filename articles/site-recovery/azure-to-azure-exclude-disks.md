---
title: Azure Site Recovery исключение дисков во время репликации виртуальных машин Azure с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как исключить диски виртуальных машин Azure во время Azure Site Recovery с помощью Azure PowerShell.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 81d22250262351e3c1bbb2fe28960b3d158bbf57
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147049"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Исключение дисков из репликации PowerShell для виртуальных машин Azure

В этой статье описывается, как исключить диски при репликации виртуальных машин Azure. Вы можете исключить диски, чтобы оптимизировать используемую пропускную способность репликации или целевые ресурсы, используемые этими дисками. В настоящее время эта возможность доступна только с помощью Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы:

- Убедитесь, что вы понимаете [архитектуру аварийного восстановления и компоненты](azure-to-azure-architecture.md).
- [Ознакомьтесь](azure-to-azure-support-matrix.md) с требованиями поддержки для всех компонентов.
- Убедитесь, что у вас есть модуль AzureRm PowerShell "AZ". Сведения об установке или обновлении PowerShell см. в разделе [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Убедитесь, что хранилище служб восстановления и защищенные виртуальные машины созданы по крайней мере один раз. Если вы еще не сделали этого, выполните процедуру [настройки аварийного восстановления для виртуальных машин Azure с помощью Azure PowerShell](azure-to-azure-powershell.md).

## <a name="why-exclude-disks-from-replication"></a>Зачем исключать диски из репликации
Возможно, потребуется исключить диски из репликации по следующим причинам:

- Виртуальная машина достигла [Azure Site Recovery ограничений для репликации скорости изменения данных](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix).

- Данные, обработанные на исключенном диске, не важны или их не нужно реплицировать.

- Вы хотите сохранить и сетевые ресурсы, не реплицируют данные.

## <a name="how-to-exclude-disks-from-replication"></a>Исключение дисков из репликации

В нашем примере мы выполним репликацию виртуальной машины, которая имеет одну операционную систему, и три диска данных в регионе "Восточная часть США" в регион "Западная часть США 2". Имя виртуальной машины — *азуредемовм*. Мы исключаем диск 1 и храним диски 2 и 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Получение сведений о виртуальных машинах для репликации

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

Получение сведений о дисках виртуальной машины. Эти сведения будут использоваться позже при запуске репликации виртуальной машины.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Репликация виртуальной машины Azure

В следующем примере предполагается, что у вас уже есть учетная запись хранения кэша, политика репликации и сопоставления. Если у вас нет этих действий, следуйте указаниям в подокне [Настройка аварийного восстановления для виртуальных машин Azure с помощью Azure PowerShell](azure-to-azure-powershell.md).

Репликация виртуальной машины Azure с *управляемыми дисками*.

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

После выполнения операции запуска репликации данные виртуальной машины реплицируются в регион восстановления.

Вы можете открыть портал Azure и просмотреть реплицированные виртуальные машины в разделе "реплицированные элементы".

Процесс репликации начинается с заполнения копии реплицируемых дисков виртуальной машины в регионе восстановления. Этот этап называется фазой начальной репликации.

После завершения начальной репликации репликация переходит на фазу разностной синхронизации. На этом этапе виртуальная машина защищена. Выберите защищенную виртуальную машину, чтобы проверить, не исключены ли какие либо диски.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о [выполнении тестовой](site-recovery-test-failover-to-azure.md)отработки отказа.
