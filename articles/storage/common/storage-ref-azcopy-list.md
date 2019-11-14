---
title: Список azcopy | Документация Майкрософт
description: В этой статье содержатся справочные сведения о команде azcopy List.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f02c1afadf18a7d3170eb178696487464e4a0bd3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034095"
---
# <a name="azcopy-list"></a>azcopy list

Выводит список сущностей в заданном ресурсе.

## <a name="synopsis"></a>Краткий обзор

В текущем выпуске поддерживаются только контейнеры больших двоичных объектов.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>Связанные концептуальные статьи

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)
- [Перенос данных с помощью AzCopy и хранилища BLOB-объектов](storage-use-azcopy-blobs.md)
- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)
- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)

## <a name="examples"></a>Примеры

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Параметры

|Параметр|ОПИСАНИЕ|
|--|--|
|-h, --help|Отображение содержимого справки для команды List.|
|--Machine-доступное для чтения|Выводит список размеров файлов в байтах.|
|--мегасимволов-единицы|Отображает единицы в заказах 1000, а не 1024.|
|--работает-высокая|Подсчитывает общее количество файлов и их размеры.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Параметр|ОПИСАНИЕ|
|---|---|
|--Cap-Мбит/с UInt32|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|

## <a name="see-also"></a>См. также

- [azcopy](storage-ref-azcopy.md)
