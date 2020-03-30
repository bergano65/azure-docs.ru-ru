---
title: азкопии энв Документы Майкрософт
description: В этой статье содержится справочная информация для команды azcopy env.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: cc0ed5f1eec76bedc21106c90e5e82332e27ce3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033750"
---
# <a name="azcopy-env"></a>azcopy env

Отображает переменные среды, которые могут настроить поведение AzCopy.

## <a name="synopsis"></a>Краткий обзор

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Если вы установите переменную среды с помощью командной строки, эта переменная будет читаема в истории командной строки. Рассмотрите возможность очистки переменных, содержащих учетные данные из истории командной строки. Чтобы не отображаться переменные в истории, можно использовать скрипт, чтобы подсказать пользователю их учетные данные и установить переменную среды.

## <a name="related-conceptual-articles"></a>Похожие концептуальные статьи

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)
- [Передача данных с помощью хранилища AzCopy и Blob](storage-use-azcopy-blobs.md)
- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)
- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)

## <a name="options"></a>Параметры

|Параметр|Описание|
|--|--|
|-h, --help|Отображает содержимое справки для команды env. |
|--шоу-чувствительный|Отображает чувствительные/секретные переменные среды.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Параметр|Описание|
|---|---|
|--кап-мбитп uint32|Ограничивает скорость передачи, в мегабитах в секунду. Пропускная часть момент за моментом может незначительно отличаться от крышки. Если эта опция установлена до нуля или опущена, пропускная часть не ограничена.|
|--выход типа строки|Формат вывода команды. Выбор включает в себя: текст, Json. Значение по умолчанию является "текст".|

## <a name="see-also"></a>См. также

- [Azcopy](storage-ref-azcopy.md)
