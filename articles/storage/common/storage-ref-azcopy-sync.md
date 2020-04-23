---
title: азкопия синхронизации Документы Майкрософт
description: В этой статье содержится справочная информация для команды синхронизации azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d855019be7f357a35a26d14e68ba3d427d984e17
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086034"
---
# <a name="azcopy-sync"></a>azcopy sync

Повторяет место исходного кода к месту назначения.

## <a name="synopsis"></a>Краткий обзор

Для сравнения используются последние измененные времена. Файл пропускается, если последнее измененное время в пункте назначения является более поздним.

Поддерживаемые пары:

- местные < > Azure Blob (можно использовать проверку подлинности SAS или OAuth)
- Azure Blob <-> Azure Blob (Источник должен включать SAS или является общедоступным; либо SAS, либо OAuth authentication могут быть использованы для назначения)
- Файл Azure < > Azure File (Источник должен включать SAS или быть общедоступным; Проверка подлинности SAS должна использоваться для назначения)

Команда синхронизации отличается от команды копирования несколькими способами:

1. По умолчанию рекурсивный флаг является верным и синхронизирует все субдиректора. Синхронизация только копирует файлы верхнего уровня в каталоге, если рекурсивный флаг является ложным.
2. При синхронизации между виртуальными каталогами добавьте в путь задняя косая черта (обратитесь к примерам), если есть капля с тем же именем, что и один из виртуальных каталогов.
3. Если флаг 'deleteDestination' настроен на истину или подсказку, синхронизация удалит файлы и капли в пункте назначения, которые не присутствуют в источнике.

## <a name="related-conceptual-articles"></a>Похожие концептуальные статьи

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)
- [Передача данных с помощью хранилища AzCopy и Blob](storage-use-azcopy-blobs.md)
- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)
- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)

### <a name="advanced"></a>Дополнительно

Если вы не указали расширение файла, AzCopy автоматически обнаруживает тип содержимого файлов при загрузке с локального диска, основываясь на расширении файла или содержимом (если расширение не указано).

Встроенная таблица поиска мала, но на Unix, она дополнена файлом mime.types локальной системы(если он доступен под одним или более из этих имен:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

На Windows типы MIME извлекаются из реестра.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Примеры

Синхронизация одного файла:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

> [!NOTE]
> Пункт назначения *должен* существовать. Используйте `azcopy copy` для копирования одного файла, который еще не существует в пункте назначения. В противном случае `Cannot perform sync due to error: sync must happen between source and destination of the same type, e.g. either file <-> file, or directory/container <-> directory/container`возникает следующая ошибка: .

То же, что и выше, но на этот раз, также вычислить MD5 хэш содержимого файла и сохранить его в качестве свойства содержания-MD5 капли:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Синхронизация всего каталога, включая его подкаталоги (обратите внимание, что рекурсивный включен по умолчанию):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

или диспетчер конфигурации служб

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Синхронизируйте только верхние файлы внутри каталога, но не его подкаталоги:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Синхронизация подмножества файлов в каталоге (например: только файлы jpg и PDF, или если имя файла "exactName"):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Синхронизация всего каталога, но исключить определенные файлы из области (например: каждый файл, который начинается с foo или заканчивается с баром):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Синхронизация одной капли:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Синхронизация виртуального каталога:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Синхронизация виртуального каталога, который имеет то же имя, что и капля (добавить задняя черта на пути для того, чтобы disambiguate):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Синхронизация каталога файлов Azure (тот же синтаксис, что и Blob):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Если флаги включения/исключения используются вместе, будут рассматриваться только файлы, соответствующие шаблонам включения, но те, которые соответствуют шаблонам исключений, всегда будут игнорироваться.

## <a name="options"></a>Параметры

**--блок-размер-mb** поплавок Используйте этот размер блока (указано в MiB) при загрузке в Azure Storage или загрузке из Azure Storage. По умолчанию автоматически рассчитывается на основе размера файла. Разрешены десятичные фракции (например: 0,25).

**--Check-md5** строка определяет, как строго MD5 хэши должны быть проверены при загрузке. Эта опция доступна только при загрузке. Доступные значения включают в себя: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (по умолчанию 'FailIfDifferent'). (по умолчанию "FailIfDifferent")

**--удалить назначения** строка определяет, следует ли удалить дополнительные файлы из назначения, которые не присутствуют в источнике. Может быть установлен на истинное, ложное или оперативное. Если настроенна на запрос, пользователю будет задан вопрос до планирования файлов и капли для удаления. (по умолчанию 'ложный'). (по умолчанию "ложный")

**--исключить атрибуты строки** (только Windows) Исключить файлы, атрибуты которых соответствуют списку атрибутов. Например: A; S; R

**--исключить-путь** строки Исключить эти пути при копировании. Эта опция не поддерживает символы подстановочных знаков (кв). Проверка приставки относительной траектории (Например: myFolder;myFolder/subDirName/file.pdf). При использовании в сочетании с обходом учетной записи пути не включают имя контейнера.

**--исключить шаблон** строки Исключить файлы, где имя совпадает со списком шаблонов. Например: \*.jpg; \*.pdf;exactName

**-h, --помощь** помощь для синхронизации

**--включить атрибуты строки** (только Windows) Включают только файлы, атрибуты которых соответствуют списку атрибутов. Например: A; S; R

**--включить шаблон** строки Включают только файлы, где имя совпадает со списком шаблонов. Например: \*.jpg; \*.pdf;exactName

**--строка уровня журнала** Определите многословность журнала для файла журнала, доступные уровни: INFO (все запросы и ответы), ВНИМАНИЕ (медленные ответы), ERROR (только не удающиеся запросы) и NONE (без журналов вывода). (по умолчанию INFO). (по умолчанию "INFO")

**--пут-md5**                     Создайте хэш MD5 каждого файла и сохраните хэш как свойство Content-MD5 капли назначения или файла. (По умолчанию хэш НЕ создается.) Доступно только при загрузке.

**--рекурсивный**                   Правда по умолчанию, посмотрите в подкаталоги рекурсивно при синхронизации между каталогами. (по умолчанию верно). (по умолчанию верно)

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Параметр|Описание|
|---|---|
|--кап-мбитп uint32|Ограничивает скорость передачи, в мегабитах в секунду. Пропускная часть момент за моментом может незначительно отличаться от крышки. Если эта опция установлена до нуля или опущена, пропускная часть не ограничена.|
|--выход типа строки|Формат вывода команды. Выбор включает в себя: текст, Json. Значение по умолчанию является "текст".|

## <a name="see-also"></a>См. также раздел

- [azcopy](storage-ref-azcopy.md)
