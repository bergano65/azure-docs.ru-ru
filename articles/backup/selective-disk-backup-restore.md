---
title: Выборочное резервное копирование и восстановление для виртуальных машин Azure
description: В этой статье вы узнаете о выборочном резервном копировании и восстановлении диска с помощью решения для резервного копирования виртуальных машин Azure.
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: references_regions
ms.openlocfilehash: 6a5e574795dfded98260da20711dab7d16cabd5b
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566239"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>Выборочное резервное копирование и восстановление для виртуальных машин Azure

Azure Backup поддерживает резервное копирование всех дисков (операционной системы и данных) на ВИРТУАЛЬНОЙ машине вместе с помощью решения для резервного копирования виртуальных машин. Теперь с помощью функций резервного копирования и восстановления выборочных дисков можно создать резервную копию подмножества дисков данных в виртуальной машине. Это обеспечивает эффективное и экономичное решение для резервного копирования и восстановления. Каждая точка восстановления содержит только диски, входящие в операцию резервного копирования. Это позволяет получить подмножество дисков, восстановленных из заданной точки восстановления во время операции восстановления. Это относится как к восстановлению из моментальных снимков, так и к хранилищу.

>[!NOTE]
>Выборочная Архивация и восстановление на виртуальных машинах Azure доступна в общедоступной предварительной версии во всех регионах.

## <a name="scenarios"></a>Сценарии

Это решение особенно полезно в следующих сценариях:

1. Если у вас есть важные данные для резервного копирования только на одном диске или в подмножестве дисков и вы не хотите создавать резервные копии остальных дисков, подключенных к виртуальной машине, чтобы снизить затраты на хранение резервных копий.
2. При наличии других решений резервного копирования для части виртуальной машины или данных. Например, если вы создаете резервную копию баз данных или данных с помощью другого решения резервного копирования рабочей нагрузки и хотите использовать резервное копирование на уровне виртуальной машины Azure для работы с остальными данными или дисками, чтобы создать эффективную и надежную систему с использованием лучших доступных возможностей.

С помощью PowerShell или Azure CLI можно настроить выборочную архивацию на диске виртуальной машины Azure.  С помощью скрипта можно включать или исключать диски данных, используя их номера LUN.  В настоящее время возможность настройки выборочного резервного копирования с помощью портал Azure ограничена параметром " **архивировать только диск ОС** ". Поэтому вы можете настроить резервное копирование виртуальной машины Azure с помощью диска операционной системы и исключить все подключенные к нему диски данных.

>[!NOTE]
> Диск ОС по умолчанию добавляется в резервную копию виртуальной машины и не может быть исключен.

## <a name="using-azure-cli"></a>Использование Azure CLI

Убедитесь, что вы используете AZ CLI версии 2.0.80 или более поздней. Версию CLI можно получить с помощью следующей команды:

```azurecli
az --version
```

Войдите в систему с ИДЕНТИФИКАТОРом подписки, в котором существует хранилище служб восстановления и виртуальная машина.

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>В каждой из приведенных ниже команд требуется только имя **resourcegroup** (не объект), соответствующее хранилищу.

### <a name="configure-backup-with-azure-cli"></a>Настройка резервного копирования с помощью Azure CLI

Во время операции настройки защиты необходимо указать параметр списка дисков с **inclusion**  /  параметром**исключения** включения, указав номера LUN дисков, которые должны быть включены в резервную копию или исключены из нее.

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

Если виртуальная машина находится не в той же группе ресурсов, что и хранилище, то Группа ресурсов, в которой было создано хранилище **, относится к** этой группе. Вместо имени виртуальной машины укажите идентификатор виртуальной машины, как показано ниже.

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id | tr -d '"') --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>Изменить защиту для уже резервных копий виртуальных машин с Azure CLI

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>Создавать резервную копию только диска ОС во время настройки резервного копирования с помощью Azure CLI

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} -- exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>Создавать резервную копию только диска ОС во время защиты от изменения с помощью Azure CLI

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>Восстановление дисков с помощью Azure CLI

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-to-staging-storage-account --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>Восстановление только диска ОС с Azure CLI

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-to-staging-storage-account --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>Получение защищенного элемента для получения сведений об исключении диска с Azure CLI

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

К защищенному элементу добавляется дополнительный параметр **дискексклусионпропертиес** , как показано ниже:

```azurecli
"extendedProperties": {
      "diskExclusionProperties": {
        "diskLunList": [
          0,
          1
        ],
        "isInclusionList": true
      }
```

### <a name="get-backup-job-with-azure-cli"></a>Получение задания резервного копирования с помощью Azure CLI

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

Эта команда позволяет получить сведения о резервных дисках и исключенных дисках, как показано ниже:

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

### <a name="list-recovery-points-with-azure-cli"></a>Перечисление точек восстановления с помощью Azure CLI

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

Это позволяет получить сведения о количестве подключенных дисков и их резервной копии на виртуальной машине.

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>Получение точки восстановления с помощью Azure CLI

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

Каждая точка восстановления содержит сведения о включенных и исключенных дисках:

```azurecli
  "recoveryPointDiskConfiguration": {
      "excludedDiskList": [
        {
          "lun": 2,
          "name": "diskextest_DataDisk_2"
        }
      ],
      "includedDiskList": [
        {
          "lun": -1,
          "name": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b"
        },
        {
          "lun": 0,
          "name": "diskextest_DataDisk_0"
        },
        {
          "lun": 1,
          "name": "diskextest_DataDisk_1"
        }
      ],
      "numberOfDisksAttachedToVm": 4,
      "numberOfDisksIncludedInBackup": 3
```

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>Удаление параметров исключения диска и получение защищенного элемента с помощью Azure CLI

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

При выполнении этих команд вы увидите `"diskExclusionProperties": null` .

## <a name="using-powershell"></a>Использование PowerShell

Убедитесь, что вы используете Azure PS версии 3.7.0 или более поздней.

### <a name="enable-backup-with-powershell"></a>Включение резервного копирования с помощью PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -DiskListSetting "Include"/"Exclude" -DisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>Создание резервной копии только диска ОС во время настройки резервного копирования с помощью PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>Получение объекта резервной копии для передачи в качестве защиты на изменение с помощью PowerShell

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $Vault.ID -FriendlyName "V2VM"
```

Необходимо передать полученный выше объект **$Item** в параметр **– Item** в следующих командлетах.

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>Изменение защиты для уже резервных копий виртуальных машин с помощью PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -DiskListSetting "Include"/"Exclude" -DisksList[Strings]   -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>Создавать резервную копию только диска ОС во время защиты от изменения с помощью PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>Сброс параметров исключения диска с помощью PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -DiskListSetting "Reset" -VaultId $targetVault.ID
```

### <a name="restore-selective-disks-with-powershell"></a>Восстановление выборочных дисков с помощью PowerShell

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [Strings]
```

### <a name="restore-only-os-disk-with-powershell"></a>Восстановление только диска ОС с помощью PowerShell

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>Использование портала Azure

С помощью портал Azure можно просмотреть включенные и Исключенные диски в области сведения о резервном копировании виртуальной машины и области сведения о задании резервного копирования.  Во время восстановления при выборе точки восстановления для восстановления можно просмотреть резервные диски в этой точке восстановления.

Здесь можно просмотреть включенные и Исключенные диски для виртуальной машины на портале в области сведения о резервном копировании виртуальной машины:

![Просмотр включенных и исключенных дисков из области сведений о резервном копировании](./media/selective-disk-backup-restore/backup-details.png)

Здесь можно просмотреть включенные и Исключенные диски в резервной копии из области сведений о задании.

![Просмотр включенных и исключенных дисков из области сведений о задании](./media/selective-disk-backup-restore/job-details.png)

Здесь можно просмотреть резервные диски во время восстановления, выбрав точку восстановления для восстановления из:

![Просмотр резервных копий дисков во время восстановления](./media/selective-disk-backup-restore/during-restore.png)

Настройка возможности резервного копирования выборочных дисков для виртуальной машины с помощью портал Azure ограничена параметром " **архивировать только диск ОС** ". Чтобы использовать резервную копию выборочных дисков в уже созданной резервной копии виртуальной машины или для расширенного включения или исключения конкретных дисков данных виртуальной машины, используйте PowerShell или Azure CLI.

>[!NOTE]
>Если данные распределяются между дисками, убедитесь, что все зависимые диски включены в резервную копию. Если не создать резервную копию всех зависимых дисков в томе, то во время восстановления том, состоящий из некоторых неархивных дисков, не будет создан.

### <a name="backup-os-disk-only-in-the-azure-portal"></a>Резервное копирование диска ОС в портал Azure

При включении резервного копирования с помощью портал Azure можно выбрать параметр **архивировать только диск ОС** . Поэтому вы можете настроить резервное копирование виртуальной машины Azure с помощью диска операционной системы и исключить все подключенные к нему диски данных.

![Настройка резервного копирования только для диска ОС](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="selective-disk-restore"></a>Выборочное восстановление диска

Выборочное восстановление диска — это дополнительная функция, получаемая при включении функции резервного копирования выборочных дисков. С помощью этой функции можно восстановить выборочные диски со всех дисков, которые были архивированы в точке восстановления. Это более эффективно и помогает экономить время в сценариях, где известно, какие диски необходимо восстановить.

- Диск операционной системы по умолчанию включен в резервную копию и восстановление виртуальной машины и не может быть исключен.
- Выборочное восстановление диска поддерживается только для точек восстановления, созданных после включения функции исключения диска.
- Резервные копии с параметром исключения диска **для** поддерживают только параметр **восстановления диска** . **Восстановление виртуальной машины** или **Замена существующих** параметров восстановления в этом случае не поддерживается.

![Параметр восстановления виртуальной машины и замены существующих недоступен во время операции восстановления](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>Ограничения

Функции резервного копирования выборочных дисков не поддерживаются для классических виртуальных машин и зашифрованных виртуальных машин. Так что виртуальные машины Azure, зашифрованные с помощью шифрования дисков Azure (ADE), используют BitLocker для шифрования виртуальных машин Windows, а функция DM-Encryption для виртуальных машин Linux не поддерживается.

Параметры восстановления для **создания новой виртуальной машины** и **замены существующих** не поддерживаются для виртуальной машины, для которой включена функция резервного копирования выборочных дисков.

## <a name="billing"></a>Выставление счетов

Резервное копирование виртуальных машин Azure соответствует существующей модели ценообразования, подробно описанной [здесь](https://azure.microsoft.com/pricing/details/backup/).

**Стоимость защищенного экземпляра (PI)** вычисляется для диска ОС только в том случае, если выбран вариант резервного копирования с использованием параметра **только диск операционной системы** .  Если вы настроили резервное копирование и выбрали по крайней мере один диск данных, стоимость PI будет рассчитана для всех дисков, подключенных к виртуальной машине. **Стоимость хранилища резервных копий** вычисляется на основе только включенных дисков, поэтому вы получаете возможность сэкономить стоимость хранилища. **Стоимость моментального снимка** всегда вычисляется для всех дисков виртуальной машины (включенных и исключенных дисков).  

## <a name="next-steps"></a>Дальнейшие действия

- [Матрица поддержки для резервного копирования виртуальных машин Azure](backup-support-matrix-iaas.md)
- [Часто задаваемые вопросы. Резервное копирование виртуальных машин Azure](backup-azure-vm-backup-faq.md)
