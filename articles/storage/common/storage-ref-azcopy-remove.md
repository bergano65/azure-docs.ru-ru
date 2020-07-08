---
title: azcopy Remove | Документация Майкрософт
description: В этой статье содержатся справочные сведения по команде azcopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 05/04/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ab085b9a41120a9f56c1c2e39a89def8c3893747
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221069"
---
# <a name="azcopy-remove"></a>azcopy remove

Удаление больших двоичных объектов или файлов из учетной записи хранения Azure.

## <a name="synopsis"></a>Краткий обзор

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Связанные концептуальные статьи

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)
- [Перенос данных с помощью AzCopy и хранилища BLOB-объектов](storage-use-azcopy-blobs.md)
- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)
- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)

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
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

Удалите весь виртуальный каталог, но исключите определенные большие двоичные объекты из области (например, каждый большой двоичный объект, начинающийся с foo или заканчивающийся на Bar):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
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

**--Delete — строка моментальных снимков** по умолчанию операция удаления завершается ошибкой, если BLOB-объект имеет моментальные снимки. Укажите "include", чтобы удалить корневой BLOB-объект и все его моментальные снимки. Кроме того, можно указать "только", чтобы удалить только моментальные снимки, но сохранив корневой большой двоичный объект.

**--Строка исключения-путь** исключает эти пути при удалении. Этот параметр не поддерживает подстановочные знаки (*). Проверяет префикс относительного пути. Например: myFolder; myFolder/Субдирнаме/file.pdf.

**--исключение-шаблон** строка исключает файлы, имя которых совпадает со списком шаблонов. Например: *. jpg;*. PDF; Ексактнаме

**--Force — if — только для чтения**    При удалении файла или папки службы файлов Azure принудительно удаляется, даже если у существующего объекта установлен атрибут "только для чтения".

**-h,--** Справка по удалению

**--include строка-Path** включает только эти пути при удалении. Этот параметр не поддерживает подстановочные знаки (*). Проверяет префикс относительного пути. Например: myFolder; myFolder/Субдирнаме/file.pdf

**--include — строка шаблона** включает только файлы, имена которых совпадают с списком шаблонов. Например: *. jpg;*. PDF; Ексактнаме

**--List-of-Files** определяет расположение файла, содержащего список удаляемых файлов и каталогов. Относительные пути должны быть разделены разрывами строк, а пути не должны быть закодированы в виде URL-адресов.

**--строка на уровне журнала** определяет уровень детализации журнала для файла журнала. Доступные уровни: INFO (все запросы и ответы), предупреждение (медленные ответы), ошибка (только неудачные запросы) и нет (нет выходных журналов). (по умолчанию "INFO") (по умолчанию "INFO")

**--recursive**                Рекурсивный просмотр подкаталогов при синхронизации между каталогами.

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Параметр|Описание:|
|---|---|
|--Cap-Мбит/с UInt32|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|
|--Trusted-Microsoft-суффикс строка   |Указывает дополнительные суффиксы домена, в которых могут быть отправлены Azure Active Directory токены входа.  Значение по умолчанию — "*. Core.Windows.NET;*". core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net ". Все перечисленные здесь значения добавляются к значениям по умолчанию. В целях безопасности следует размещать только Microsoft Azureные домены. Несколько записей разделяются точкой с запятой.|

## <a name="see-also"></a>См. также

- [azcopy](storage-ref-azcopy.md)
