---
title: Azure file share – failed to delete files from Azure file share (Общая папка Azure — сбой удаления файлов)
description: Выявление и устранение неполадок при удалении файлов из файлового ресурса Azure.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 1c50f54491e0284005c4b762b0c4b35a8460f5ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85511980"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure file share – failed to delete files from Azure file share (Общая папка Azure — сбой удаления файлов)

Сбой удаления файлов из общей папки Azure может иметь несколько симптомов:

**Симптом 1.**

Не удалось удалить файл в общей папке Azure из-за одной из двух следующих проблем:

* Файл, помеченный для удаления
* Указанный ресурс может использоваться клиентом SMB

**Симптом 2.**

Недостаточно квот для обработки команды

## <a name="cause"></a>Причина

Ошибка 1816 возникает при достижении верхнего предела одновременных открытых дескрипторов, разрешенных для файла, на компьютере, на котором размонтируется общая папка. Дополнительные сведения см. в статье [Контрольный список производительности и масштабируемости службы хранилища Azure](https://docs.microsoft.com/azure/storage/blobs/storage-performance-checklist).

## <a name="resolution"></a>Решение

Сократите число параллельно открытых дескрипторов, закрыв некоторые дескрипторы.

## <a name="prerequisite"></a>Предварительное требование

### <a name="install-the-latest-azure-powershell-module"></a>Установите последнюю версию модуля Azure PowerShell

* [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Подключение к Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Выберите подписку целевой учетной записи хранения:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Создайте контекст для целевой учетной записи хранения:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Получение текущих открытых дескрипторов файлового ресурса:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Пример результата:

|хандлеид|путь|ClientIp|клиентпорт|опентиме|ластреконнекттиме|FileId|ParentId|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Новая папка/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Закройте открытый маркер:

Чтобы закрыть открытый маркер, используйте следующую команду:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Дальнейшие действия

* [Устранение неполадок службы файлов Azure в Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Устранение неполадок службы файлов Azure в Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Устранение неполадок службы "Синхронизация файлов Azure"](storage-sync-files-troubleshoot.md)