---
title: NVv4-серия - Виртуальные машины Azure
description: Спецификации для VMs серии NVv4.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 0295ed7d44d64fcc1aeb68e1beaa37987b177edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273847"
---
# <a name="nvv4-series"></a>Серия NVv4 

Виртуальные машины серии NVv4 оснащены процессорами [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) и процессорами AMD EPYC 7V12 (Рим). С NVv4-серии Azure представляет виртуальные машины с частичными графическими процессорами. Выберите правильный размер виртуальной машины для GPU ускоренных графических приложений и виртуальных настольных компьютеров, начиная с 1/8th gPU с 2 GiB буфер кадра для полного графического процессора с 16 GiB буфер кадра. Виртуальные машины NVv4 в настоящее время поддерживают только гостевую операционную систему Windows.

<br>

Хранилище класса Premium: поддерживается

Премиум кэширование хранилища: Поддерживается

Миграция в реальном времени: не поддерживается

Сохранение памяти Обновления: Не поддерживается

| Размер | vCPU | Память, ГиБ | Временное хранилище (SSD): ГиБ | Графический процессор | Память GPU: ГиБ | Максимальное число дисков данных | Максимальное число сетевых адаптеров |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> VVv4-серии VMs оснащены технологией одновременных многопоточных технологий AMD

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы

Чтобы воспользоваться возможностями графического процессора VMs серии Azure NVv4 под управлением Windows, необходимо установить драйверы графического процессора AMD.

Чтобы установить драйверы AMD GPU вручную, [см. N-серию AMD GPU установки драйвера для Windows](./windows/n-series-amd-driver-setup.md) для поддерживаемых операционных систем, драйверов, установки и проверки шагов.

## <a name="other-sizes"></a>Остальные размеры

- [Общее назначение](sizes-general.md)
- [Оптимизирована память](sizes-memory.md)
- [Оптимизированные для операций в хранилище](sizes-storage.md)
- [Оптимизированные для GPU](sizes-gpu.md)
- [Для высокопроизводительных вычислений](sizes-hpc.md)
- [Предыдущие поколения](sizes-previous-gen.md)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.
