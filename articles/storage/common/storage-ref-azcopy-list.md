---
title: Список azcopy | Документация Майкрософт
description: В этой статье содержатся справочные сведения о команде azcopy List.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8c40241c49917c6db6663f346aed0ec5d3b96be7
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195949"
---
# <a name="azcopy-list"></a>Список azcopy

Выводит список сущностей в заданном ресурсе.

## <a name="synopsis"></a>Краткий обзор

В текущем выпуске поддерживаются только контейнеры больших двоичных объектов.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="examples"></a>Примеры

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Параметры

|Параметр|Описание|
|--|--|
|-h, --help|Отображение содержимого справки для команды List.|
|--Machine-доступное для чтения|Выводит список размеров файлов в байтах.|
|--мегасимволов-единицы|Отображает единицы в заказах 1000, а не 1024.|
|--работает-высокая|Подсчитывает общее количество файлов и их размеры.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Параметр|Описание|
|---|---|
|--Cap-Мбит/с UInt32|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|

## <a name="see-also"></a>См. также

- [azcopy](storage-ref-azcopy.md)
