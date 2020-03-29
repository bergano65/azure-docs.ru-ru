---
title: HBv2-серия - Виртуальные машины Azure
description: Спецификации для VMs серии HBv2.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: eea649610ca53ccbb98b5ca361555280dcd3dafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164803"
---
# <a name="hbv2-series"></a>Серия HBv2

Имм-технологий серии HBv2 оптимизированы для приложений, управляемых пропускной способностью памяти, таких как динамика жидкости, анализ конечных элементов и моделирование резервуаров. HBv2 VMs оснащены 120 процессорными ядрами AMD EPYC 7742, 4 ГБ оперативной памяти на ядро процессора и без одновременных многопоточных потоков. Каждый HBv2 VM обеспечивает до 340 ГБ/сек пропускной способности памяти и до 4 терафлоперов fp64.

Хранилище класса Premium: поддерживается

Миграция в реальном времени: не поддерживается

Сохранение памяти Обновления: Не поддерживается

| Размер | vCPU | Процессор | Память (ГБ) | Пропускная способность памяти GB/s | Базовая частота процессора (ГГц) | Частота всех ядер (ГГц, пик) | Одноядерная частота (ГГц, пик) | Производительность RDMA (Gb/s) | Поддержка MPI | Темп хранения (GB) | Максимальное число дисков данных | МАКС Ethernet NICs |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | All | 480 х 960 | 8 | 1 |


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