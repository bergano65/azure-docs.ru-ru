---
title: азкопия сделать Документы Майкрософт
description: Эта статья предоставляет справочную информацию для команды azcopy make.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 67d685684c5227377a0f8a7e822a06e785a69d89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034048"
---
# <a name="azcopy-make"></a>azcopy make

Создает емкость или совместное представление файлов.

## <a name="synopsis"></a>Краткий обзор

Создайте емкость контейнера или раздел файла, представленный URL-адресом данного ресурса.

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Похожие концептуальные статьи

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)
- [Передача данных с помощью хранилища AzCopy и Blob](storage-use-azcopy-blobs.md)
- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)
- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)

## <a name="examples"></a>Примеры

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>Параметры

|Параметр|Описание|
|--|--|
|-h, --help|Показать содержимое справки для команды make. |
|--квота-gb uint32|Упогоняет максимальный размер доли в гигабайтах (GiB), 0 означает, что вы принимаете по умолчанию квоту файлосервиса.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Параметр|Описание|
|---|---|
|--кап-мбитп uint32|Ограничивает скорость передачи, в мегабитах в секунду. Пропускная часть момент за моментом может незначительно отличаться от крышки. Если эта опция установлена до нуля или опущена, пропускная часть не ограничена.|
|--выход типа строки|Формат вывода команды. Выбор включает в себя: текст, Json. Значение по умолчанию является "текст".|

## <a name="see-also"></a>См. также

- [Azcopy](storage-ref-azcopy.md)
