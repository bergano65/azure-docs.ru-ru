---
title: azcopy Remove | Документация Майкрософт
description: В этой статье содержатся справочные сведения по команде azcopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: fc23afb9a407fc2e6689c5c8766cb4beba868269
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513441"
---
# <a name="azcopy-remove"></a>azcopy remove

Удаление больших двоичных объектов или файлов из учетной записи хранения Azure.

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

Удалите определенные большие двоичные объекты и виртуальные каталоги путем размещения в файле относительных путей (не закодированных в URL-адресах):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Удалите один файл из учетной записи хранения BLOB-объектов с иерархическим пространством имен (Включение или исключение не поддерживается).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Удаление одного каталога. учетная запись хранения BLOB-объектов с иерархическим пространством имен (Включение или исключение не поддерживается):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Параметры

**--Строка исключения-путь**      Исключите эти пути при удалении. Этот параметр не поддерживает подстановочные знаки (*). Проверяет префикс относительного пути. Например: myFolder; myFolder/Субдирнаме/File. PDF.

**--исключение-шаблон** строка исключает файлы, имя которых совпадает со списком шаблонов. Например: *. jpg;* . PDF; Ексактнаме

**-h,--** Справка по удалению

**--include строка-Path** включает только эти пути при удалении. Этот параметр не поддерживает подстановочные знаки (*). Проверяет префикс относительного пути. Например: myFolder; myFolder/Субдирнаме/File. PDF

**--include — строка шаблона** включает только файлы, имена которых совпадают с списком шаблонов. Например: *. jpg;* . PDF; Ексактнаме

**--List-of-Files** определяет расположение файла, содержащего список удаляемых файлов и каталогов. Относительные пути должны быть разделены разрывами строк, а пути не должны быть закодированы в виде URL-адресов.

**--строка на уровне журнала** определяет уровень детализации журнала для файла журнала. Доступные уровни: INFO (все запросы и ответы), предупреждение (медленные ответы), ошибка (только неудачные запросы) и нет (нет выходных журналов). (по умолчанию "INFO") (по умолчанию "INFO")

**--recursive**                Рекурсивный просмотр подкаталогов при синхронизации между каталогами.

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Вариант|Описание|
|---|---|
|--Cap-Мбит/с UInt32|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|

## <a name="see-also"></a>Дополнительные материалы

- [azcopy](storage-ref-azcopy.md)
