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
ms.openlocfilehash: e143a5e82b817aaba37750a8cce08e3f74f0abc8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84220026"
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

|Параметр|Описание:|
|--|--|
|--Destination-строка SAS|Целевой SAS назначения для заданного ИД задания.|
|--исключение строки|Фильтр: исключите эти неудачные перемещения при возобновлении задания. Файлы должны быть разделены символом ";".|
|-h, --help|Отображение содержимого справки для команды возобновления.|
|--включить строку|Фильтр: включить только эти неудачные операции передач при возобновлении задания. Файлы должны быть разделены символом ";".|
|--Source-строка SAS |исходный SAS источника для заданного ИД задания.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Параметр|Описание:|
|---|---|
|--Cap-Мбит/с UInt32|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|
|--Trusted-Microsoft-суффикс строка   |Указывает дополнительные суффиксы домена, в которых могут быть отправлены Azure Active Directory токены входа.  Значение по умолчанию — "*. Core.Windows.NET;*". core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net ". Все перечисленные здесь значения добавляются к значениям по умолчанию. В целях безопасности следует размещать только Microsoft Azureные домены. Несколько записей разделяются точкой с запятой.|

## <a name="see-also"></a>См. также

- [azcopy jobs](storage-ref-azcopy-jobs.md)
