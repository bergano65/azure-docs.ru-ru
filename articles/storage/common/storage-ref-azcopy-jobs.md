---
title: задания azcopy | Документация Майкрософт
description: В этой статье содержатся справочные сведения о команде azcopy jobs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: bd5469691f8d859bfcbca7479cdcb2e468a94e26
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195845"
---
# <a name="azcopy-jobs"></a>задания azcopy

Подкоманды, связанные с управлением заданиями.

## <a name="examples"></a>Примеры

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>Параметры

|Параметр|Описание|
|--|--|
|-h, --help|Отображение содержимого справки для команды задания.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Параметр|Описание|
|---|---|
|--Cap-Мбит/с UInt32|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|

## <a name="see-also"></a>См. также

- [azcopy](storage-ref-azcopy.md)
- [список заданий azcopy](storage-ref-azcopy-jobs-list.md)
- [возобновление заданий azcopy](storage-ref-azcopy-jobs-resume.md)
- [отображаются задания azcopy](storage-ref-azcopy-jobs-show.md)
