---
title: возобновление заданий azcopy | Документация Майкрософт
description: В этой статье содержатся справочные сведения о команде возобновления заданий azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 3ee7879475801660b5200dddca88a0a81b2b6b9b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74034139"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Возобновляет существующее задание с заданным ИДЕНТИФИКАТОРом задания.

## <a name="synopsis"></a>Краткий обзор

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Связанные концептуальные статьи

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)
- [Перенос данных с помощью AzCopy и хранилища BLOB-объектов](storage-use-azcopy-blobs.md)
- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)
- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)

## <a name="options"></a>Параметры

|Параметр|Описание|
|--|--|
|--Destination-строка SAS|Целевой SAS назначения для заданного ИД задания.|
|--исключение строки|Фильтр: исключите эти неудачные перемещения при возобновлении задания. Файлы должны быть разделены символом ";".|
|-h, --help|Отображение содержимого справки для команды возобновления.|
|--включить строку|Фильтр: включить только эти неудачные операции передач при возобновлении задания. Файлы должны быть разделены символом ";".|
|--Source-строка SAS |исходный SAS источника для заданного ИД задания.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Параметр|Описание|
|---|---|
|--Cap-Мбит/с UInt32|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|

## <a name="see-also"></a>См. также

- [azcopy jobs](storage-ref-azcopy-jobs.md)
