---
title: азкопия рабочих мест чистой Документы Майкрософт
description: В этой статье содержится справочная информация для чистой команды заданий azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033719"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Удалить все файлы журнала и спланировать все задания

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Похожие концептуальные статьи

- [Get started with AzCopy](storage-use-azcopy-v10.md) (Начало работы с AzCopy)
- [Передача данных с помощью хранилища AzCopy и Blob](storage-use-azcopy-blobs.md)
- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md) (Передача данных с помощью AzCopy и хранилища файлов)
- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)

## <a name="examples"></a>Примеры

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Параметры

**-h, --помощь**                Помощь для очистки.

**--с-статус** строки Только удалить задания с этим статусом, доступные значения: Отменено, Завершено, Не удалось, InProgress, Все (по умолчанию "Все")

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

**--кап-мбитп uint32**      Ограничивает скорость передачи, в мегабитах в секунду. Пропускная часть момент за моментом может незначительно отличаться от крышки. Если эта опция установлена до нуля или опущена, пропускная часть не ограничена.

**--тип вывода** Формат вывода команды. Выбор включает в себя: текст, Json. Значение по умолчанию — это «текст». (по умолчанию "текст")

## <a name="see-also"></a>См. также

- [azcopy jobs](storage-ref-azcopy-jobs.md)
