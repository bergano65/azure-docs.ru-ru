---
title: Список azcopy | Документация Майкрософт
description: В этой статье содержатся справочные сведения о команде azcopy List.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b7348e3790166e1a1aecab422e571b8f2fc7cd5f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513516"
---
# <a name="azcopy-list"></a>azcopy list

Выводит список сущностей в заданном ресурсе.

## <a name="synopsis"></a>Краткий обзор

В текущем выпуске поддерживаются только контейнеры больших двоичных объектов.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="examples"></a>Примеры

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Параметры

|Вариант|Описание|
|--|--|
|-h, --help|Отображение содержимого справки для команды List.|
|--Machine-доступное для чтения|Выводит список размеров файлов в байтах.|
|--мегасимволов-единицы|Отображает единицы в заказах 1000, а не 1024.|
|--работает-высокая|Подсчитывает общее количество файлов и их размеры.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Вариант|Описание|
|---|---|
|--Cap-Мбит/с UInt32|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|

## <a name="see-also"></a>Дополнительные материалы

- [azcopy](storage-ref-azcopy.md)
