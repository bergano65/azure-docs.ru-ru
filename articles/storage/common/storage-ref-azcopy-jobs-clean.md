---
title: Очистка заданий azcopy | Документация Майкрософт
description: В этой статье содержатся справочные сведения по команде azcopy jobs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 31529155ee44b2bcfad90e8634053403dfe8fc8c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518516"
---
# <a name="azcopy-jobs-clean"></a>Очистка заданий azcopy

Удалить все файлы журналов и планов для всех заданий

```
azcopy jobs clean [flags]
```

## <a name="examples"></a>Примеры

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Параметры

**-h,--Справка**                Справка по очистке.

**параметр--WITH-Status** удаляет только задания с этим состоянием, доступные значения: отменено, завершено, сбой, выполняется, все (по умолчанию "все")

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

**--Cap-Мбит/с UInt32**      Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.

**--выходной** формат строки выходных данных команды. Среди вариантов: Text, JSON. Значение по умолчанию — Text. ("текст" по умолчанию)

## <a name="see-also"></a>Дополнительные материалы

- [задания azcopy](storage-ref-azcopy-jobs.md)
