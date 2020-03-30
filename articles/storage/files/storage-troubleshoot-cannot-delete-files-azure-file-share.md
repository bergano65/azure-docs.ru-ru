---
title: Azure file share – failed to delete files from Azure file share (Общая папка Azure — сбой удаления файлов)
description: Определите и устраните неустранение файлов из раздела файлов Azure.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: d3a3763a8964810626bcdc47da230a9ee406f1f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196479"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure file share – failed to delete files from Azure file share (Общая папка Azure — сбой удаления файлов)

Неспособность удалить файлы из раздела файлов Azure может иметь несколько симптомов:

**Симптом 1:**

Не удалось удалить файл в лазурной части файла из-за одной из двух проблем ниже:

* Файл, отмеченный для удаления
* Указанный ресурс может быть использоваться клиентом SMB

**Симптом 2:**

Недостаточно квот для обработки команды

## <a name="cause"></a>Причина

Ошибка 1816 происходит, когда вы достигаете верхнего предела одновременных открытых ручек, разрешенных для файла, на компьютере, где устанавливается доля файла. Для получения дополнительной информации посетите [контрольный список производительности и масштабируемости хранилища Azure.](https://docs.microsoft.com/azure/storage/blobs/storage-performance-checklist)

## <a name="resolution"></a>Решение

Уменьшите количество одновременных открытых ручек, закрыв некоторые ручки.

## <a name="prerequisite"></a>Предварительные требования

### <a name="install-the-latest-azure-powershell-module"></a>Установка последнего модуля Azure PowerShell

* [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Подключение к Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Выберите подписку целевого хранилища:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Создайте контекст для целевой учетной записи хранилища:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Получите текущие открытые ручки общего файла:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Пример результата:

|HandleId|путь|ClientIp|КлиентПорт|Открытое время|Последнее время подключения|FileId|Parentid|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53|0|0|9507758546259807489|
|259101229136|Новая папка/test.zip|10.222.10.123|62758|2019-10-05|12:36:29|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Закройте открытую ручку:

Чтобы закрыть открытую ручку, используйте следующую команду:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Дальнейшие действия

* [Устранение неполадок службы файлов Azure в Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Устранение неполадок службы файлов Azure в Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Устранение неполадок службы "Синхронизация файлов Azure"](storage-sync-files-troubleshoot.md)