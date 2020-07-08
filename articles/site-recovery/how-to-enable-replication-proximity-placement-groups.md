---
title: Репликация виртуальных машин Azure, выполняемых в группах размещения близкого взаимодействия
description: Узнайте, как реплицировать виртуальные машины Azure, выполняемые в группах размещения близкого взаимодействия, с использованием Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 05/25/2020
ms.openlocfilehash: 9fabf6cf4c8a3afc2d119fca2c8cdc2526ddbebb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84415871"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>Репликация виртуальных машин Azure, выполняемых в группах размещения близкого взаимодействия, в другой регион

В этой статье описывается репликация, отработка отказа и восстановление виртуальных машин, выполняемых в группах размещения близкого взаимодействия, в дополнительном регионе.

[Группы размещения близкого взаимодействия](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups-portal) — это реализованная в виртуальных машинах Azure возможность логической группировки, благодаря которой можно сократить сетевую задержку приложений при обмене данными между виртуальными машинами. Если виртуальные машины развернуты в одной группе размещения близкого взаимодействия, они физически расположены как можно ближе друг к другу. Группы размещения близкого взаимодействия особенно полезны при выполнении рабочих нагрузок, чувствительных к величине задержки.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Аварийное восстановление с использованием групп размещения близкого взаимодействия

В стандартном сценарии виртуальные машины могут работать в группах размещения близкого взаимодействия во избежание сетевой задержки между различными уровнями приложения. Хотя это может обеспечить оптимальную сетевую задержку для вашего приложения, нужно защитить эти приложения с помощью Site Recovery от любых сбоев на региональном уровне. Site Recovery реплицирует данные из одного региона в другой регион Azure и вызывает компьютеры в регионе аварийного восстановления в случае сбоя.

## <a name="considerations"></a>Рекомендации

- Лучше всего организовать отработку отказа/восстановление виртуальных машин в группе размещения близкого взаимодействия. Однако если восстановить виртуальную машину внутри групп размещения близкого взаимодействия в процессе отработки отказа не удастся, отработка отказа все равно произойдет, но виртуальные машины будут созданы вне группы размещения.
-  Если группа доступности закреплена в группе размещения близкого взаимодействия и во время отработки отказа/восстановления виртуальных машин в группе доступности действует ограничение на распределение, виртуальные машины будут созданы за пределами группы доступности и группы размещения близкого взаимодействия.
-  Site Recovery для групп размещения близкого взаимодействия не поддерживается для неуправляемых дисков.

> [!Note]
> Azure Site Recovery не поддерживает восстановление размещения с управляемых дисков для сценариев Hyper-V в Azure. Следовательно, восстановление размещения из группы размещения в Azure на Hyper-V не поддерживается.

## <a name="prerequisites"></a>Предварительные требования

1. Убедитесь в наличии модуля Azure PowerShell Az. Если вам необходимо установить или обновить Azure PowerShell, ознакомьтесь с этим [руководством по установке и настройке Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>Настройка Site Recovery для виртуальных машин в группе размещения близкого взаимодействия

### <a name="azure-to-azure"></a>Из Azure в Azure

1. [Войдите](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#sign-in-to-your-microsoft-azure-subscription) в свою учетную запись и настройте подписку.
2. Получите сведения о виртуальной машине, которую вы планируете реплицировать, как указано [здесь](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#get-details-of-the-virtual-machine-to-be-replicated).
3. [Создайте](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-recovery-services-vault) хранилище служб восстановления и [задайте](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#set-the-vault-context) контекст хранилища.
4. Подготовьте хранилище к запуску виртуальной машины репликации. Это включает создание [объекта фабрики службы](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) для основного региона и региона восстановления.
5. [Создайте](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-site-recovery-protection-container-in-the-primary-fabric) защитный контейнер Site Recovery для основной фабрики и фабрики восстановления.
6. [Создайте](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-replication-policy) политику репликации.
7. Создайте сопоставление защитных контейнеров (основного контейнера и контейнера восстановления), выполнив [эти](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) шаги, а также сопоставление защитных контейнеров для восстановления, следуя инструкциям [здесь](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover).
8. Создайте учетную запись хранения кэша, выполнив [следующие](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-cache-storage-account-and-target-storage-account) действия.
9. Создайте необходимые сетевые сопоставления, как указано [здесь](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-network-mappings).
10. Для репликации виртуальной машины Azure с управляемыми дисками используйте командлет PowerShell ниже. 

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id ` -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id ` -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $recPpg.Id
```
Когда начальная репликация успешно завершится, все данные виртуальной машины будут реплицированы в регион восстановления.

Процесс репликации начинается с того, что в регионе восстановления создаются копии реплицируемых дисков виртуальной машины. Этот этап называется начальной репликацией.

По окончании первоначальной репликации начинается процесс разностной синхронизации. На этом этапе виртуальная машина считается защищенной и для нее можно выполнять тестовую отработку отказа. Когда завершится начальная репликация, реплицируемый элемент, представляющий виртуальную машину, примет состояние репликации "Защищено".

Чтобы отследить текущее состояние и работоспособность репликации для виртуальной машины, получите сведения о репликации для соответствующего защищенного элемента. 

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. Чтобы выполнить тестовую отработку отказа, проверить и очистить данные после тестовой отработки, выполните [эти](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#do-a-test-failover-validate-and-cleanup-test-failover) действия.
12. Для отработки отказа выполните действия [здесь](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#fail-over-to-azure).
13. Для защиты и восстановления исходного региона используйте следующий командлет PowerShell:

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```
14. Для отключения репликации выполните описанные [здесь](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#disable-replication) действия.

### <a name="vmware-to-azure"></a>VMware — Azure

1. Убедитесь, что для аварийного восстановления в Azure [подготовлены локальные серверы VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises).
2. Войдите в свою учетную запись и настройте подписку, как указано [здесь](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#log-into-azure).
3. [Настройте](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#set-up-a-recovery-services-vault) хранилище служб восстановления и [задайте контекст восстановления](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#set-the-vault-context).
4. [Подтвердите](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#validate-vault-registration) регистрацию хранилища.
5. [Создайте](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#create-a-replication-policy) политику репликации.
6. [Добавьте](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#add-a-vcenter-server-and-discover-vms) сервер vCenter, обнаруживайте виртуальные машины и [создавайте](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#create-storage-accounts-for-replication) учетные записи хранения для репликации.
7. Сведения о репликации виртуальных машин VMware доступны здесь. Кроме того, необходимо выполнить следующий командлет PowerShell:

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $recPpg.Id
```
8. Состояние репликации и работоспособность репликации виртуальной машины можно проверить с помощью командлета Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
9. Настройте параметры отработки отказа, выполняя описанные [здесь](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#configure-failover-settings) действия.
10. [Выполните](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#run-a-test-failover) тестовую отработку отказа. 
11. Выполните отработку отказа в Azure, следуя инструкциям [здесь](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#fail-over-to-azure).

### <a name="hyper-v-to-azure"></a>Hyper-V в Azure

1. Убедитесь, что для аварийного восстановления в Azure [подготовлены локальные серверы Hyper-V](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial).
2. [Выполните вход](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-1-sign-in-to-your-azure-account) в Azure.
3. [Настройте](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-2-set-up-the-vault) хранилище и [задайте](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-3-set-the-recovery-services-vault-context) контекст хранилища служб восстановления.
4. [Создайте](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-4-create-a-hyper-v-site) сайт Hyper-V.
5. [Установите](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-5-install-the-provider-and-agent) поставщик и агент.
6. [Создайте](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-6-create-a-replication-policy) политику репликации.
7. Включите репликацию, выполнив инструкции ниже. 
    
    а. Получите защищаемый элемент, соответствующий виртуальной машине, которую необходимо защитить, выполнив следующую команду:

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. Включите защиту виртуальной машины. Если к виртуальной машине, которую необходимо защитить, подключено несколько дисков, нужно указать диск операционной системы (ОС) с помощью параметра OSDiskName.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $recPpg.Id
    ```
    c. Дождитесь, пока виртуальные машины перейдут в защищенное состояние после начальной репликации. Это может занять некоторое время в зависимости от таких факторов, как объем данных для репликации и доступная пропускная способность вышестоящих служб в Azure. Когда виртуальные машины перейдут в защищенное состояние, параметры State и StateDescription задания обновятся следующим образом: 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. Обновите свойства восстановления (например, размер роли виртуальной машины) и сеть Azure, к которой подключится сетевой адаптер виртуальной машины после отработки отказа.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. Выполните тестовую [отработку отказа](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-8-run-a-test-failover).


## <a name="next-steps"></a>Дальнейшие действия

Для повторной защиты и отработки отказа VMware в Azure выполните описанные [здесь](https://docs.microsoft.com/azure/site-recovery/vmware-azure-prepare-failback) действия.

Чтобы выполнить отработку отказа для Hyper-V в Azure, выполните действия, описанные [здесь](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover), а чтобы выполнить восстановление, выполните действия, описанные [здесь](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-failback).

Дополнительные сведения см. в статье [Отработка отказа в Site Recovery](site-recovery-failover.md).
