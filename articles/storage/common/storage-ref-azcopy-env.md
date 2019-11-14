---
title: azcopy env | Документация Майкрософт
description: В этой статье содержатся справочные сведения о команде azcopy env.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: cc0ed5f1eec76bedc21106c90e5e82332e27ce3c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033750"
---
# <a name="azcopy-env"></a>azcopy env

Показывает переменные среды, которые могут настраивать поведение AzCopy.

## <a name="synopsis"></a>Краткий обзор

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Если задать переменную среды с помощью командной строки, эта переменная будет доступна для чтения в журнале командной строки. Рассмотрите возможность очистки переменных, содержащих учетные данные из журнала командной строки. Чтобы переменные отображались в журнале, можно использовать сценарий, чтобы запросить у пользователя учетные данные и задать переменную среды.

## <a name="related-conceptual-articles"></a>Связанные концептуальные статьи

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)
- [Перенос данных с помощью AzCopy и хранилища BLOB-объектов](storage-use-azcopy-blobs.md)
- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)
- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)

## <a name="options"></a>Параметры

|Параметр|ОПИСАНИЕ|
|--|--|
|-h, --help|Отображает содержимое справки для команды env. |
|--показывать с учетом|Отображает конфиденциальные и секретные переменные среды.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Параметр|ОПИСАНИЕ|
|---|---|
|--Cap-Мбит/с UInt32|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|

## <a name="see-also"></a>См. также

- [azcopy](storage-ref-azcopy.md)
