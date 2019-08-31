---
title: azcopy doc | Документация Майкрософт
description: В этой статье содержатся справочные сведения о команде azcopy doc.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d1f57ae40b47c1d910ba20ae8a8f19cdc6908d6b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196040"
---
# <a name="azcopy-doc"></a>azcopy doc

Создает документацию для инструмента в формате Markdown.

## <a name="synopsis"></a>Краткий обзор

Создает документацию для средства в формате Markdown и сохраняет их в указанном расположении.

По умолчанию файлы хранятся в папке с именем doc в текущем каталоге.

```azcopy
azcopy doc [flags]
```

## <a name="options"></a>Параметры

|Параметр|Описание|
|--|--|
|-h, --help|Отображает содержимое справки для команды doc.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Параметр|Описание|
|---|---|
|--Cap-Мбит/с UInt32|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|

## <a name="see-also"></a>См. также

- [azcopy](storage-ref-azcopy.md)
