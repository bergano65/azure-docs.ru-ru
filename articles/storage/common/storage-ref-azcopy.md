---
title: azcopy | Документация Майкрософт
description: В этой статье содержатся справочные сведения о команде azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4da9206f4500941179d781a0fe2a57ad15d7393d
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195897"
---
# <a name="azcopy"></a>azcopy

AzCopy — это программа командной строки, которая перемещает данные в службу хранилища Azure и из нее.

## <a name="synopsis"></a>Краткий обзор

Общий формат команд: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Чтобы сообщить о проблемах или узнать больше об этом средстве, [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy)см. раздел.

## <a name="options"></a>Параметры

|Параметр|Описание|
|---|---|
|--Cap-Мбит/с UInt32|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|-h, --help|Отображает содержимое справки для azcopy.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|

## <a name="see-also"></a>См. также

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)
- [копирование azcopy](storage-ref-azcopy-copy.md)
- [azcopy doc](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [задания azcopy](storage-ref-azcopy-jobs.md)
- [Список azcopy](storage-ref-azcopy-list.md)
- [имя входа azcopy](storage-ref-azcopy-login.md)
- [azcopy Logout](storage-ref-azcopy-logout.md)
- [azcopy](storage-ref-azcopy-make.md)
- [azcopy удалить](storage-ref-azcopy-remove.md)
- [Синхронизация azcopy](storage-ref-azcopy-sync.md)
