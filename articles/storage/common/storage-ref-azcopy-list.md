---
title: азкопии список Документы Майкрософт
description: В этой статье содержится справочная информация для команды списка azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f02c1afadf18a7d3170eb178696487464e4a0bd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034095"
---
# <a name="azcopy-list"></a>azcopy list

Перечисляет сущности в данном ресурсе.

## <a name="synopsis"></a>Краткий обзор

В текущем выпуске поддерживаются только контейнеры Blob.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>Похожие концептуальные статьи

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)
- [Передача данных с помощью хранилища AzCopy и Blob](storage-use-azcopy-blobs.md)
- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)
- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)

## <a name="examples"></a>Примеры

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Параметры

|Параметр|Описание|
|--|--|
|-h, --help|Отображаем содержимое справки для команды списка.|
|--машина-читается|Списки размеров файлов в байтах.|
|--мега-единицы|Отображает единицы в заказах 1000, а не 1024.|
|--бег-подсчет|Подсчитывает общее количество файлов и их размеры.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Параметр|Описание|
|---|---|
|--кап-мбитп uint32|Ограничивает скорость передачи, в мегабитах в секунду. Пропускная часть момент за моментом может незначительно отличаться от крышки. Если эта опция установлена до нуля или опущена, пропускная часть не ограничена.|
|--выход типа строки|Формат вывода команды. Выбор включает в себя: текст, Json. Значение по умолчанию является "текст".|

## <a name="see-also"></a>См. также

- [Azcopy](storage-ref-azcopy.md)
