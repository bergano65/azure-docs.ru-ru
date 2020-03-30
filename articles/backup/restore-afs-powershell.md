---
title: Восстановление файлов Azure с помощью PowerShell
description: В этой статье узнайте, как восстановить файлы Azure с помощью службы резервного копирования Azure и PowerShell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 99aeaa6173bb5336e6e1719a9fc0df0c668374e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086825"
---
# <a name="restore-azure-files-with-powershell"></a>Восстановление файлов Azure с помощью PowerShell

В этой статье объясняется, как восстановить всю общую часть файлов или определенные файлы из точки восстановления, созданной службой [резервного копирования Azure](backup-overview.md) с помощью Azure Powershell.

Можно восстановить всю общую часть файла или определенные файлы на этой доле. Можно восстановить исходное местоположение или в другое местоположение.

> [!WARNING]
> Убедитесь, что версия PS обновлена до минимальной версии для 'Az.RecoveryServices 2.6.0' для резервных upups AFS. Для получения более подробной информации обратитесь к [разделу](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) с изложением требования к этому изменению.

## <a name="fetch-recovery-points"></a>Получение точек восстановления

Используйте [Get-AzRecoveryServicesBackUpRecoveryRecoveryPointPoint,](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) чтобы перечислить все точки восстановления для резервного элемента.

В следующем сценарии:

* Переменная **$rp** представляет собой массив точек восстановления для выбранного элемента резервного копирования за последние семь дней.
* Массив сортируется по времени в обратном порядке, так что последняя точка восстановления получает индекс **0**.
* Используйте стандартное индексирование массива PowerShell для выбора точки восстановления.
* В примере **$rp** выбирает последнюю точку восстановления.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Выход аналогичен следующему.

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

После выбора соответствующей точки восстановления вы восстанавливаете долю файла или файл в исходное местоположение или в другое местоположение.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Восстановление общего элемента файла Azure в альтернативное местоположение

Используйте [Restore-AzRecoveryServicesBackupUpItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) для восстановления выбранной точки восстановления. Укажите эти параметры для определения альтернативного местоположения:

* **TargetStorageAccountName**: Учетная запись хранилища, с которой восстанавливается резервное содержимое. Целевая учетная запись хранения должна быть в том же расположении, что и хранилище.
* **TargetFileShareName**: Файл делится в целевом хранилище, к которому восстанавливается резервное содержимое.
* **TargetFolder**: Папка под файлом общего использования, к которому восстанавливаются данные. Если содержимое резервной копии необходимо восстановить в корневую папку, укажите значения целевой папки в виде пустой строки.
* **ResolveConflict**: Инструкция, если есть конфликт с восстановленными данными. Принимает **перезаписать** или **пропустить**.

Выполнить cmdlet с параметрами следующим образом:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Эта команда возвращает задания для отслеживания с идентификатором, как показано в следующем примере.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Восстановление файла Azure в альтернативном месте

Используйте [Restore-AzRecoveryServicesBackupUpItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) для восстановления выбранной точки восстановления. Укажите эти параметры, чтобы определить альтернативное местоположение и однозначно определить файл, который вы хотите восстановить.

* **TargetStorageAccountName**: Учетная запись хранилища, с которой восстанавливается резервное содержимое. Целевая учетная запись хранения должна быть в том же расположении, что и хранилище.
* **TargetFileShareName**: Файл делится в целевом хранилище, к которому восстанавливается резервное содержимое.
* **TargetFolder**: Папка под файлом общего использования, к которому восстанавливаются данные. Если содержимое резервной копии необходимо восстановить в корневую папку, укажите значения целевой папки в виде пустой строки.
* **SourceFilePath**: Абсолютный путь файла, который будет восстановлен в рамках общего файла, как строка. Этот же путь используется в командлете PowerShell **Get-AzStorageFile**.
* **SourceFileType**: Выбран ли каталог или файл. Принимает **каталог** или **файл**.
* **ResolveConflict**: Инструкция, если есть конфликт с восстановленными данными. Принимает **перезаписать** или **пропустить**.

Дополнительные параметры (SourceFilePath и SourceFileType) связаны только с отдельным файлом, который вы хотите восстановить.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Эта команда возвращает задание с идентификатором для отслеживания, как показано в предыдущем разделе.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Восстановление акций и файлов файлов Azure в исходное местоположение

При восстановлении исходного местоположения не нужно указывать параметры назначения и целевой цели. Нужно только указать **ResolveConflict**.

#### <a name="overwrite-an-azure-file-share"></a>Перезапись файлового ресурса Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Перезапись файла Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="next-steps"></a>Дальнейшие действия

[Узнайте о](restore-afs.md) восстановлении файлов Azure на портале Azure.
