---
title: Восстановление файлов Azure с помощью PowerShell
description: Из этой статьи вы узнаете, как восстановить файлы Azure с помощью службы Azure Backup и PowerShell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 99aeaa6173bb5336e6e1719a9fc0df0c668374e2
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086825"
---
# <a name="restore-azure-files-with-powershell"></a>Восстановление файлов Azure с помощью PowerShell

В этой статье объясняется, как восстановить всю общую папку или отдельные файлы из точки восстановления, созданной службой [Azure Backup](backup-overview.md) с помощью Azure PowerShell.

Можно восстановить весь файловый ресурс или отдельные файлы в общей папке. Можно выполнить восстановление в исходное расположение или в другое расположение.

> [!WARNING]
> Убедитесь, что версия PS обновлена до минимальной версии для "az. RecoveryServices 2.6.0" для резервных копий AFS. Дополнительные сведения см. в [разделе](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) , описывающем требования к этому изменению.

## <a name="fetch-recovery-points"></a>Получение точек восстановления

Используйте [Get-азрековерисервицесбаккупрековерипоинт](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) , чтобы получить список всех точек восстановления для архивированного элемента.

В следующем скрипте:

* Переменная **$RP** представляет собой массив точек восстановления для выбранного элемента резервного копирования за последние семь дней.
* Массив сортируется по времени в обратном порядке, так что последняя точка восстановления получает индекс **0**.
* Используйте стандартное индексирование массива PowerShell для выбора точки восстановления.
* В примере **$rp[0]** выбирает последнюю точку восстановления.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Выходные данные похожи на приведенные ниже.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

После выбора соответствующей точки восстановления вы восстанавливаете общую папку или файл в исходное расположение или в альтернативное расположение.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Восстановление файлового ресурса Azure в альтернативное расположение

Используйте [RESTORE-азрековерисервицесбаккупитем](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) для восстановления в выбранную точку восстановления. Укажите эти параметры, чтобы определить альтернативное расположение:

* **Таржетсторажеаккаунтнаме**: учетная запись хранения, в которую восстанавливается резервное содержимое. Целевая учетная запись хранения должна быть в том же расположении, что и хранилище.
* **Таржетфилешаренаме**. файловые ресурсы в целевой учетной записи хранения, в которую восстанавливается резервное содержимое.
* **TargetFolder**: папка в общей папке, в которую восстанавливаются данные. Если содержимое резервной копии необходимо восстановить в корневую папку, укажите значения целевой папки в виде пустой строки.
* **ResolveConflict**: инструкция при конфликте с восстановленными данными. Принимает **перезаписать** или **пропустить**.

Выполните командлет с параметрами следующим образом:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Эта команда возвращает задания для отслеживания с идентификатором, как показано в следующем примере.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Восстановление файла Azure в альтернативное расположение

Используйте [RESTORE-азрековерисервицесбаккупитем](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) для восстановления в выбранную точку восстановления. Укажите эти параметры, чтобы определить альтернативное расположение, и, чтобы однозначно определить файл, который требуется восстановить.

* **Таржетсторажеаккаунтнаме**: учетная запись хранения, в которую восстанавливается резервное содержимое. Целевая учетная запись хранения должна быть в том же расположении, что и хранилище.
* **Таржетфилешаренаме**. файловые ресурсы в целевой учетной записи хранения, в которую восстанавливается резервное содержимое.
* **TargetFolder**: папка в общей папке, в которую восстанавливаются данные. Если содержимое резервной копии необходимо восстановить в корневую папку, укажите значения целевой папки в виде пустой строки.
* **SourceFilePath**: абсолютный путь к файлу, восстанавливаемому в общей папке, в виде строки. Этот же путь используется в командлете PowerShell **Get-AzStorageFile**.
* **Саурцефилетипе**: указывает, выбран ли каталог или файл. Принимает **папка** или **файл**.
* **ResolveConflict**: инструкция при конфликте с восстановленными данными. Принимает **перезаписать** или **пропустить**.

Дополнительные параметры (SourceFilePath и Саурцефилетипе) относятся только к отдельному файлу, который требуется восстановить.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Эта команда возвращает задание с ИДЕНТИФИКАТОРом для трассировки, как показано в предыдущем разделе.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Восстановить файловые ресурсы и файлы Azure в исходном расположении

При восстановлении в исходное расположение не нужно указывать параметры, связанные с назначением и целевым объектом. Нужно только указать **ResolveConflict**.

#### <a name="overwrite-an-azure-file-share"></a>Перезапись файлового ресурса Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Перезапись файла Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="next-steps"></a>Дальнейшие действия

[Дополнительные сведения о](restore-afs.md) восстановлении файлов Azure в портал Azure.
