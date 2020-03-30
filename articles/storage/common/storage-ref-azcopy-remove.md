---
title: азкопия удалить Документы Майкрософт
description: Эта статья содержит справочную информацию для команды удаления azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033999"
---
# <a name="azcopy-remove"></a>azcopy remove

Удаление капли или файлов из учетной записи хранения Azure.

## <a name="synopsis"></a>Краткий обзор

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Похожие концептуальные статьи

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)
- [Передача данных с помощью хранилища AzCopy и Blob](storage-use-azcopy-blobs.md)
- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)
- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)

## <a name="examples"></a>Примеры

Удалите одну каплю с помощью SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Удалите весь виртуальный каталог с помощью SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Удалите только верхние капли внутри виртуального каталога, но не его подкаталоги:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Удалите подмножество капли в виртуальном каталоге (например: только файлы jpg и pdf, или если имя капли "exactName"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Удалите весь виртуальный каталог, но исключите определенные капли из сферы (например: каждый капли, которая начинается с foo или заканчивается бар):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Удалите конкретные капли и виртуальные каталоги, поместив их относительные пути (НЕ ЗАкодированные URL) в файл:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Удалите один файл из учетной записи Blob Storage, которая имеет иерархическое пространство имен (включить/исключить не поддерживается).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Удалите один каталог учетной записи Blob Storage с иерархическим пространством имен (включить/исключить не поддерживается):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Параметры

**--исключить-путь строки**      Исключите эти пути при удалении. Эта опция не поддерживает символы подстановочных знаков (кв). Проверка приставки относительного пути. Например: myFolder;myFolder/subDirName/file.pdf.

**--исключить шаблон** строки Исключить файлы, где имя совпадает со списком шаблонов. Например: *.jpg;*. pdf;exactName

**-h, --помощь** помощь для удаления

**--включить-путь** строки Включают только эти пути при удалении. Эта опция не поддерживает символы подстановочных знаков (кв). Проверка приставки относительного пути. Например: myFolder;myFolder/subDirName/file.pdf

**--включить шаблон** строки Включают только файлы, где имя совпадает со списком шаблонов. Например: *.jpg;*. pdf;exactName

**--список файлов** строка определяет местоположение файла, который содержит список файлов и каталогов, которые будут удалены. Относительные пути должны быть разграничены разрывами строк, а пути НЕ должны быть закодированы URL-адресом.

**--строка уровня журнала** Определите многословность журнала для файла журнала. Доступные уровни включают: INFO (все запросы/ответы), ВНИМАНИЕ (медленные ответы), ERROR (только невыполненные запросы) и NONE (нет журналов вывода). (по умолчанию 'INFO') (по умолчанию "INFO")

**--рекурсивный**                Посмотрите в подкаталоги рекурсивно при синхронизации между каталогами.

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Параметр|Описание|
|---|---|
|--кап-мбитп uint32|Ограничивает скорость передачи, в мегабитах в секунду. Пропускная часть момент за моментом может незначительно отличаться от крышки. Если эта опция установлена до нуля или опущена, пропускная часть не ограничена.|
|--выход типа строки|Формат вывода команды. Выбор включает в себя: текст, Json. Значение по умолчанию является "текст".|

## <a name="see-also"></a>См. также

- [Azcopy](storage-ref-azcopy.md)
