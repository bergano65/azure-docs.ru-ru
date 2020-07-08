---
title: Серия NVv4
description: Спецификации виртуальных машин серии NVv4.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: e12124ee2d87a14c6e7cb1704fbe50c6300c1240
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045728"
---
# <a name="nvv4-series"></a>Серия NVv4 

Виртуальные машины серии NVv4 на базе процессоров [AMD Radeon ПОРЫВОМ MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU и AMD ЕПИК 7V12 (Рим). Azure Series NVv4 предоставляет виртуальные машины с частичными GPU. Выберите виртуальную машину с нужным размером для графических приложений с ускорением GPU и виртуальных рабочих столов, начиная с 1/8 GPU с 2 гибным буфером кадров, до полного графического процессора с 16 гибным буфером кадров. В настоящее время виртуальные машины NVv4 поддерживают только гостевую операционную систему Windows.

<br>

Хранилище класса "Премиум":  Поддерживается

Кэширование в хранилище класса Premium:  Поддерживается

Динамическая миграция: Не поддерживается

Обновления с сохранением памяти: Не поддерживается

| Размер | vCPU | Память: ГиБ | Временное хранилище (SSD): ГиБ | Графический процессор | Память GPU: ГиБ | Максимальное число дисков данных | Максимальное количество сетевых адаптеров/ожидаемая пропускная способность сети (Мбит/с) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 / 1000 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 / 2000 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 / 4000 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8/8000 |

<sup>1</sup> виртуальная машина серии NVv4 — технология AMD параллельной многопоточности

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы

Чтобы воспользоваться возможностями GPU виртуальных машин серии NVv4 Azure под управлением Windows, необходимо установить драйверы AMD GPU.

Чтобы вручную установить драйверы AMD GPU, см. раздел [Установка драйвера GPU AMD серии N для Windows](./windows/n-series-amd-driver-setup.md) для поддерживаемых операционных систем, драйверов, установки и шагов проверки.

## <a name="other-sizes"></a>Остальные размеры

- [Универсальные](sizes-general.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для хранилища](sizes-storage.md)
- [Оптимизированные для GPU](sizes-gpu.md)
- [Для высокопроизводительных вычислений](sizes-hpc.md)
- [Предыдущие поколения](sizes-previous-gen.md)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.
