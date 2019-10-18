---
title: Удаление заданий azcopy | Документация Майкрософт
description: В этой статье содержатся справочные сведения по команде azcopy Jobs Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 82c399580322334e67c0c9c2b88d1edf6f175e0c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518295"
---
# <a name="azcopy-jobs-remove"></a>Удаление заданий azcopy

Удалить все файлы, связанные с заданным ИДЕНТИФИКАТОРом задания.

> [!NOTE] 
> Можно настроить расположение для сохранения файлов журнала и плана. Дополнительные сведения см. в описании команды [azcopy env](storage-ref-azcopy-env.md) .

```
azcopy jobs remove [jobID] [flags]
```

## <a name="examples"></a>Примеры

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Параметры

**-h,--Справка**                Справка по удалению.

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

**--Cap-Мбит/с UInt32**      Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.

**--выходной** формат строки выходных данных команды. Среди вариантов: Text, JSON. Значение по умолчанию — Text. ("текст" по умолчанию)

## <a name="see-also"></a>Дополнительные материалы

- [задания azcopy](storage-ref-azcopy-jobs.md)
