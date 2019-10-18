---
title: задания azcopy | Документация Майкрософт
description: В этой статье содержатся справочные сведения о команде azcopy jobs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 2422b16eb89ef6f1a6a1eb703d88f0ff2b76422e
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514732"
---
# <a name="azcopy-jobs"></a>azcopy jobs

Подкоманды, связанные с управлением заданиями.

## <a name="examples"></a>Примеры

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>Параметры

|Вариант|Описание|
|--|--|
|-h, --help|Отображение содержимого справки для команды задания.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Вариант|Описание|
|---|---|
|--Cap-Мбит/с UInt32|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|

## <a name="see-also"></a>Дополнительные материалы

- [azcopy](storage-ref-azcopy.md)
- [список заданий azcopy](storage-ref-azcopy-jobs-list.md)
- [возобновление заданий azcopy](storage-ref-azcopy-jobs-resume.md)
- [отображаются задания azcopy](storage-ref-azcopy-jobs-show.md)
