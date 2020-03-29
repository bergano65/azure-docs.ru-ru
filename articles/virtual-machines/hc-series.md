---
title: HC-серия - Виртуальные машины Azure
description: Спецификации для VMs-серии HC.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: cc25fb9b21d535ef07bcfae673be48216427b370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164786"
---
# <a name="hc-series"></a>Серия HC

ВМ серии HC оптимизированы для приложений, управляемых плотными вычислениями, такими как неявный анализ конечных элементов, молекулярная динамика и вычислительная химия. HC VMs оснащены 44 процессорами Intel Xeon Platinum 8168, 8 ГБ оперативной памяти на ядро процессора и отсутствием гиперпотока. Платформа Intel Xeon Platinum поддерживает богатую экосистему программных средств Intel, таких как Библиотека ядра Intel Math.

ACU: 297-315

Хранилище класса Premium: поддерживается

Кэширование в хранилище класса Premium: поддерживается

Миграция в реальном времени: не поддерживается

Сохранение памяти Обновления: Не поддерживается

| Размер | vCPU | Процессор | Память (ГБ) | Пропускная способность памяти GB/s | Базовая частота процессора (ГГц) | Частота всех ядер (ГГц, пик) | Одноядерная частота (ГГц, пик) | Производительность RDMA (Gb/s) | Поддержка MPI | Темп хранения (GB) | Максимальное число дисков данных | МАКС Ethernet NICs |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3,7 | 100 | All | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Остальные размеры

- [Общее назначение](sizes-general.md)
- [Оптимизирована память](sizes-memory.md)
- [Оптимизированные для операций в хранилище](sizes-storage.md)
- [Оптимизированные для GPU](sizes-gpu.md)
- [Для высокопроизводительных вычислений](sizes-hpc.md)
- [Предыдущие поколения](sizes-previous-gen.md)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.