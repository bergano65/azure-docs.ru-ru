---
title: Создание azcopy | Документация Майкрософт
description: В этой статье содержатся справочные сведения о команде azcopy make.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ebed5c8dbe5001e9beab17bdbff41610277143b2
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514691"
---
# <a name="azcopy-make"></a>azcopy make

Создает контейнер или общую папку.

## <a name="synopsis"></a>Краткий обзор

Создайте контейнер или общую папку, представленную указанным URL-адресом ресурса.

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="examples"></a>Примеры

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>Параметры

|Вариант|Описание|
|--|--|
|-h, --help|Отображение содержимого справки для команды make. |
|--UInt32 с квотой в ГБ|Указывает максимальный размер общей папки в гигабайтах (гиб), 0 означает, что квота файловой службы по умолчанию принимается.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Вариант|Описание|
|---|---|
|--Cap-Мбит/с UInt32|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|

## <a name="see-also"></a>Дополнительные материалы

- [azcopy](storage-ref-azcopy.md)
