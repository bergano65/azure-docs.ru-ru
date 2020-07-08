---
title: Управление резервными копиями файловых ресурсов Azure с помощью PowerShell
description: Узнайте, как использовать PowerShell для управления и мониторинга файловых ресурсов Azure, созданных службой Azure Backup.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 6ee5fb92e4a66a9d6db66514f966c3650d3a4f13
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83201963"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Управление резервными копиями файловых ресурсов Azure с помощью PowerShell

В этой статье описывается, как использовать Azure PowerShell для управления и мониторинга файловых ресурсов Azure, резервное копирование которых выполняется службой Azure Backup.

> [!WARNING]
> Убедитесь, что версия PS обновлена до минимальной версии для "az. RecoveryServices 2.6.0" для резервных копий AFS. Дополнительные сведения см. в [разделе](backup-azure-afs-automation.md#important-notice-backup-item-identification) , описывающем требования к этому изменению.

## <a name="modify-the-protection-policy"></a>Изменение политики защиты

Чтобы изменить политику, используемую для резервного копирования файлового ресурса Azure, используйте [Enable-азрековерисервицесбаккуппротектион](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Укажите соответствующий элемент резервного копирования и новую политику резервного копирования.

В следующем примере политика защиты **testAzureFS** изменяется с **dailyafs** на **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Отслеживание заданий резервного копирования и восстановления

Операции резервного копирования и восстановления по запросу возвращают задание вместе с ИДЕНТИФИКАТОРом, как показано при [выполнении резервного копирования по запросу](backup-azure-afs-automation.md#trigger-an-on-demand-backup). Используйте командлет [Get-азрековерисервицесбаккупжобдетаилс](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) для отслеживания хода выполнения задания и сведений о нем.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```

## <a name="stop-protection-on-a-file-share"></a>Остановка защиты файлового ресурса

Есть два способа отключить защиту файловых ресурсов Azure:

* Завершите все будущие задания резервного копирования и *удалите* все точки восстановления.
* Останавливать все будущие задания резервного копирования, но *оставить* точки восстановления

При хранении точек восстановления в хранилище могут возникать затраты, поскольку базовые моментальные снимки, созданные Azure Backup, будут сохранены. Тем не менее, при необходимости вы можете восстановить файловый ресурс позже, если вы хотите покинуть точки восстановления. Сведения о затратах на уход точек восстановления см. в [сведениях о ценах](https://azure.microsoft.com/pricing/details/storage/files/). Если вы решили удалить все точки восстановления, вы не сможете восстановить общую папку.

## <a name="stop-protection-and-retain-recovery-points"></a>Отключение защиты и сохранение точек восстановления

Чтобы отключить защиту при хранении данных, используйте командлет [Disable-азрековерисервицесбаккуппротектион](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) .

В следующем примере останавливается Защита для общей папки *афсфилешаре* с сохранением всех точек восстановления:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

Атрибут идентификатора задания в выходных данных соответствует ИДЕНТИФИКАТОРу задания, созданного службой архивации для операции "отключить защиту". Чтобы отвести состояние задания, используйте командлет [Get-азрековерисервицесбаккупжоб](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-3.3.0) .

## <a name="stop-protection-without-retaining-recovery-points"></a>Отключение защиты без удержания точек восстановления

Чтобы отключить защиту без удержания точек восстановления, используйте командлет [Disable-азрековерисервицесбаккуппротектион](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) и добавьте параметр **-ремоверековерипоинтс** .

В следующем примере останавливается Защита для общей папки *афсфилешаре* без удержания точек восстановления:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>Дальнейшие шаги

[Дополнительные сведения об](manage-afs-backup.md) управлении резервными копиями файловых ресурсов Azure см. в портал Azure.
