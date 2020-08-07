---
title: Виртуальные машины Azure серии H
description: Спецификации для виртуальных машин серии H.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: e9f876f3d20af01867283f550590b3af23dec662
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926626"
---
# <a name="h-series"></a>Серия H

Виртуальные машины серии H оптимизированы для приложений, которые управляются высокой частотой ЦП или большими объемами памяти на базовые требования. Виртуальные машины серии H, компоненты 8 или 16 процессоров Intel Xeon 3 2667 v3, до 14 ГБ ОЗУ на ядро ЦП и без технологии Hyper-Threading. Функции серии H 56 ГБ/с Mellanox FDR InfiniBand в неблокирующей конфигурации дерева FAT для обеспечения постоянной производительности RDMA. Виртуальные машины серии H поддерживают Intel MPI 5. x и MS-MPI.

ACU: 290–300

Хранилище класса "Премиум":  Не поддерживается

Кэширование в хранилище класса Premium: не поддерживается

Динамическая миграция: Не поддерживается

Обновления с сохранением памяти: Не поддерживается

| Размер | vCPU | Процессор | Память (ГБ) | Пропускная способность памяти ГБ/с | Базовая частота ЦП (ГГц) | Частота ядер (ГГц, пик) | Частота с одним ядром (ГГц, пик) | Производительность RDMA (ГБ/с) | Поддержка MPI | Хранилище Temp (ГБ) | Максимальное число дисков данных | Максимальная пропускная способность дисков, операций ввода-вывода в секунду | Максимальное число сетевых адаптеров Ethernet |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon, версия 3, 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32x500 | 2 |
| Standard_H16  | 16 | Intel Xeon, версия 3, 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |
| Standard_H8m  | 8  | Intel Xeon, версия 3, 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32x500 | 2 |
| Standard_H16m | 16 | Intel Xeon, версия 3, 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon, версия 3, 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon, версия 3, 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |

<sup>1</sup> для приложений MPI сеть с выделенной внутренней сетью RDMA включена в сети FDR InfiniBand.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

> [!NOTE]
> Среди [виртуальных машин, поддерживающих RDMA](sizes-hpc.md#rdma-capable-instances), Серия H не поддерживает SR-IOV. Поэтому поддерживаемые [образы виртуальных машин](./workloads/hpc/configure.md#vm-images), требования к [драйверу InfiniBand](./workloads/hpc/enable-infiniband.md) и поддерживаемые [библиотеки MPI](./workloads/hpc/setup-mpi.md) ОТЛИЧАЮТСЯ от ВИРТУАЛЬНЫХ машин с поддержкой SR-IOV.

## <a name="other-sizes"></a>Остальные размеры

- [Универсальные](sizes-general.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для хранилища](sizes-storage.md)
- [Оптимизированные для GPU](sizes-gpu.md)
- [Для высокопроизводительных вычислений](sizes-hpc.md)
- [Предыдущие поколения](sizes-previous-gen.md)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [настройке виртуальных машин](./workloads/hpc/configure.md), [включении INFINIBAND](./workloads/hpc/enable-infiniband.md), [настройке MPI](./workloads/hpc/setup-mpi.md) и оптимизации приложений HPC для [рабочих нагрузок](./workloads/hpc/overview.md)Azure в HPC.
- Ознакомьтесь с последними объявлениями и некоторыми примерами HPC и результатами в [блогах сообщества разработчиков Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Для более высокого уровня архитектуры выполнения рабочих нагрузок HPC см. статью [высокопроизводительные вычисления (HPC) в Azure](/azure/architecture/topics/high-performance-computing/).
- Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.
