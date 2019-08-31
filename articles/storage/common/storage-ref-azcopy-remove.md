---
title: azcopy Remove | Документация Майкрософт
description: В этой статье содержатся справочные сведения по команде azcopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0cc366ab2cdad9c7258dca905d8f4a06472119fe
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195910"
---
# <a name="azcopy-remove"></a>azcopy удалить

Удаляет сущности из Azure Storage Blob, файла и Azure Data Lake Storage 2-го поколения.

## <a name="synopsis"></a>Краткий обзор

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="examples"></a>Примеры

Удаление одного большого двоичного объекта с SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Удаление всего виртуального каталога с помощью SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Удалять только верхние большие двоичные объекты внутри виртуального каталога, но не его вложенных каталогов:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Удаление подмножества больших двоичных объектов в виртуальном каталоге (например, только файлы JPG и PDF или имя большого двоичного объекта "Ексактнаме"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Удалите весь виртуальный каталог, но исключите определенные большие двоичные объекты из области (например, каждый большой двоичный объект, начинающийся с foo или заканчивающийся на Bar):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Удалить один файл из Data Lake Storage 2-го поколения (включение и исключение не поддерживается):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Удаление одного каталога из Data Lake Storage 2-го поколения (включение и исключение не поддерживается):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Параметры

|Параметр|Описание|
|--|--|
|--исключение строки|Исключение файлов, имена которых совпадают с именем списка шаблонов. Например: *. jpg;* . PDF; Ексактнаме|
|-h, --help|Отображение содержимого справки для команды "Удалить".|
|--включить строку|Включайте только файлы, имена которых совпадают с именем списка шаблонов. Например: *. jpg;* . PDF; Ексактнаме|
|--Строка уровня журнала|Укажите уровень детализации журнала для файла журнала. Доступные уровни включают: СВЕДЕНИЯ (все запросы и ответы), предупреждение (медленные ответы), ошибка (только неудачные запросы) и нет (нет выходных журналов). (по умолчанию "INFO")|
|--recursive|Рекурсивный просмотр подкаталогов при синхронизации между каталогами.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Параметр|Описание|
|---|---|
|--Cap-Мбит/с UInt32|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|

## <a name="see-also"></a>См. также

- [azcopy](storage-ref-azcopy.md)
