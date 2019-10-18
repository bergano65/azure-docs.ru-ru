---
title: azcopy doc | Документация Майкрософт
description: В этой статье содержатся справочные сведения о команде azcopy doc.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b11d831be847716d4edfac4f9a0725e07a60c52a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513867"
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

|Вариант|Описание|
|--|--|
|-h, --help|Отображает содержимое справки для команды doc.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Вариант|Описание|
|---|---|
|--Cap-Мбит/с UInt32|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|

## <a name="see-also"></a>Дополнительные материалы

- [azcopy](storage-ref-azcopy.md)
