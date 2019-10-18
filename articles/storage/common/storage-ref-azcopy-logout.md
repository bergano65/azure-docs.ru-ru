---
title: azcopy Logout | Документация Майкрософт
description: В этой статье содержатся справочные сведения о команде azcopy Logout.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 84b65470b12212eb1038e18bd442ff07511a5c1a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513497"
---
# <a name="azcopy-logout"></a>azcopy logout

Записывает пользователя в журнал и завершает доступ к ресурсам службы хранилища Azure.

## <a name="synopsis"></a>Краткий обзор

Эта команда удалит все кэшированные данные входа для текущего пользователя.

```azcopy
azcopy logout [flags]
```

## <a name="options"></a>Параметры

|Вариант|Описание|
|--|--|
|-h, --help|Отображение содержимого справки для команды Logout.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Вариант|Описание|
|---|---|
|--Cap-Мбит/с UInt32|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|

## <a name="see-also"></a>Дополнительные материалы

- [azcopy](storage-ref-azcopy.md)
