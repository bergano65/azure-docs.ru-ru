---
title: azcopy | Документация Майкрософт
description: В этой статье содержатся справочные сведения о команде azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 5da3a2e5d003a191bff66af6599cae4d34ab60c6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038089"
---
# <a name="azcopy"></a>azcopy

AzCopy — это программа командной строки, которая перемещает данные в службу хранилища Azure и из нее.

## <a name="synopsis"></a>Краткий обзор

Общий формат команд: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Чтобы сообщить о проблемах или узнать больше об этом средстве, см. [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy).

## <a name="related-conceptual-articles"></a>Связанные концептуальные статьи

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)
- [Перенос данных с помощью AzCopy и хранилища BLOB-объектов](storage-use-azcopy-blobs.md)
- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)
- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)

## <a name="options"></a>Параметры

**--Cap-Мбит/с UInt32**   Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.

**-h,--Справка** Справка по azcopy
      
**--Output-Type**  Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — Text. ("текст" по умолчанию)

## <a name="see-also"></a>См. также

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)
- [azcopy Bench](storage-ref-azcopy-bench.md)
- [копирование azcopy](storage-ref-azcopy-copy.md)
- [azcopy doc](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [задания azcopy](storage-ref-azcopy-jobs.md)
- [Очистка заданий azcopy](storage-ref-azcopy-jobs-clean.md)
- [список заданий azcopy](storage-ref-azcopy-jobs-list.md)
- [Удаление заданий azcopy](storage-ref-azcopy-jobs-remove.md)
- [возобновление заданий azcopy](storage-ref-azcopy-jobs-resume.md)
- [отображаются задания azcopy](storage-ref-azcopy-jobs-show.md)
- [Список azcopy](storage-ref-azcopy-list.md)
- [имя входа azcopy](storage-ref-azcopy-login.md)
- [azcopy Logout](storage-ref-azcopy-logout.md)
- [azcopy](storage-ref-azcopy-make.md)
- [azcopy удалить](storage-ref-azcopy-remove.md)
- [Синхронизация azcopy](storage-ref-azcopy-sync.md)
