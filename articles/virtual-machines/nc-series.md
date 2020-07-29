---
title: Виртуальные машины Azure серии NC
description: Спецификации виртуальных машин серии NC.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: bd62d7f7c04896a8a62ab1290ddbc9370934d333
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286246"
---
# <a name="nc-series"></a>Серия NC

Виртуальные машины серии NC работают на базе карты [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) и процессора Intel Xeon 3 – 2690 v3 (Haswell). Пользователи могут обрабатывать данные быстрее, используя ядра CUDA для работы с приложениями для поиска источников энергии, а также для моделирования аварий, отрисовки с построением хода луча, проведения глубинного обучения и многих других задач. Конфигурация NC24r обеспечивает низкую задержку и высокоскоростной сетевой интерфейс. Она специально ориентирована на тесно взаимосвязанные параллельные вычисления.

Хранилище класса "Премиум":  Не поддерживается

Кэширование в хранилище класса Premium:  Не поддерживается

Динамическая миграция: Не поддерживается

Обновления с сохранением памяти: Не поддерживается

| Размер | vCPU | Память: ГиБ | Временное хранилище (SSD): ГиБ | Графический процессор | Память GPU: ГиБ | Максимальное число дисков данных | Максимальное число сетевых адаптеров |
|---|---|---|---|---|---|---|---|
| Standard_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standard_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standard_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 графический процессор — половина графической карты K80.

*С поддержкой RDMA

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы

Чтобы воспользоваться преимуществами возможностей GPU виртуальных машин Azure серии N, необходимо установить драйверы GPU NVIDIA.

[Расширение драйвера GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) устанавливает необходимые драйверы CUDA или GRID NVIDIA на виртуальную машину серии N. Для установки расширения и управления им можно использовать портал Azure или такие инструменты, как Azure PowerShell и шаблоны Azure Resource Manager. Сведения о поддерживаемых операционных системах и этапах развертывания см. в [документации по расширению драйвера GPU NVIDIA](./extensions/hpccompute-gpu-windows.md). Общие сведения о расширениях виртуальных машин см. в статье [Расширения и компоненты виртуальных машин Azure](./extensions/overview.md).

Если вы решили установить драйверы NVIDIA GPU вручную, см. раздел [Установка драйвера GPU серии n для Windows](./windows/n-series-driver-setup.md) или [Установка драйвера GPU для Linux](./linux/n-series-driver-setup.md) для поддерживаемых операционных систем, драйверов, установки и проверки.

## <a name="other-sizes"></a>Остальные размеры

- [Универсальные](sizes-general.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для хранилища](sizes-storage.md)
- [Оптимизированные для GPU](sizes-gpu.md)
- [Для высокопроизводительных вычислений](sizes-hpc.md)
- [Предыдущие поколения](sizes-previous-gen.md)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.
