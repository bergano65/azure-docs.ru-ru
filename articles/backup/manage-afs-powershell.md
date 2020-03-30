---
title: Управление резервными копированиями файлов Azure с Помощью PowerShell
description: Узнайте, как использовать PowerShell для управления и мониторинга файлов Azure, поддерживаемых службой резервного копирования Azure.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a9dc421db740963fc5cd11e868eb383694376ce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083164"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Управление резервными копированиями файлов Azure с Помощью PowerShell

В этой статье описывается, как использовать Azure PowerShell для управления и мониторинга файлов Azure, которые поддерживаются службой резервного копирования Azure.

> [!WARNING]
> Убедитесь, что версия PS обновлена до минимальной версии для 'Az.RecoveryServices 2.6.0' для резервных upups AFS. Для получения более подробной информации обратитесь к [разделу](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) с изложением требования к этому изменению.

## <a name="modify-the-protection-policy"></a>Изменение политики защиты

Чтобы изменить политику, используемую для резервного копирования раздела файла Azure, используйте [Enable-AzRecoveryServicesBackUpProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0) Укажите соответствующий элемент резервного копирования и новую политику резервного копирования.

В следующем примере политика защиты **testAzureFS** изменяется с **dailyafs** на **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Отслеживание заданий резервного копирования и восстановления

Резервное копирование по требованию и восстановление операций возвращают задание вместе с идентификатором, как показано при [запуске резервного копирования по требованию.](backup-azure-afs-automation.md#trigger-an-on-demand-backup) Для отслеживания хода выполнения работ и деталей и сведений и данных используйте [Get-AzRecoveryServicesupupJobJobJobJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0)

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

* Остановить все будущие задания резервного копирования и *удалить* все точки восстановления
* Остановить все будущие резервные задания, но *оставить* точки восстановления

Возможно, это может привести к удорожанию точек восстановления в хранилище, так как основные моментальные снимки, созданные резервным копированием Azure, будут сохранены. Однако преимущество множа, связанного с выходом из точек восстановления, заключается в том, что при желании можно восстановить общую часть файла позже. Подробную информацию о стоимости выхода из пунктов восстановления можно узнать в [деталях ценообразования.](https://azure.microsoft.com/pricing/details/storage/files/) Если вы решите удалить все точки восстановления, вы не сможете восстановить общий доступ файла.

## <a name="stop-protection-and-retain-recovery-points"></a>Остановить защиту и сохранить точки восстановления

Чтобы остановить защиту при сохранении данных, используйте Смдлет [От"-AzRecovery-BackUpProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0)

Следующий пример останавливает защиту для общего файла *afsfileshare,* но сохраняет все точки восстановления:

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

Атрибут ИДента вакансий на выходе соответствует идентификатору задания, созданного службой резервного копирования для операции «защита остановки». Чтобы отследить состояние задания, используйте Смдлет [Get-AzRecoveryServicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-3.3.0)

## <a name="stop-protection-without-retaining-recovery-points"></a>Остановить защиту без сохранения точек восстановления

Чтобы остановить защиту, не сохраняя точек восстановления, используйте смдлет [Ddlet От"-AzRecoveryServicesBackUpProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) и добавьте параметр **-RemoveRecoveryPoints.**

Следующий пример останавливает защиту для совместной акции *файла afsfileshare* без сохранения точек восстановления:

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

## <a name="next-steps"></a>Дальнейшие действия

[Узнайте об](manage-afs-backup.md) управлении резервными копированиями файлов Azure на портале Azure.
