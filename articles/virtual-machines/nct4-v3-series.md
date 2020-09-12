---
title: НКАС T4 серии v3
description: Спецификации для виртуальных машин серии НКАС T4 v3.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: vikancha
ms.openlocfilehash: af9f7eb21d533bc5fb365e7cbf1fb8fc18184fa7
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89375233"
---
# <a name="ncast4_v3-series-in-preview"></a>Серии NCasT4_v3 (Предварительная версия) 

Виртуальные машины серии NCasT4_v3 на базе процессоров [NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU и AMD ЕПИК 7V12 (Рим). Компоненты виртуальных машин до 4 видеопроцессоров NVIDIA T4 с 16 ГБ памяти каждый, до 64 процессоров AMD ЕПИК 7V12 (Рим) и 440 гиб системной памяти. Эти виртуальные машины идеально подходят для выполнения рабочих нагрузок в МАШИНном обучении и AI с использованием CUDA, TensorFlow, Pytorch, Caffe и других платформ или рабочих нагрузок графики с помощью технологии сетки NVIDIA. Серии NCasT4_v3 идеально подходят для выполнения рабочих нагрузок вывода.

Вы можете [Отправить запрос](https://aka.ms/NCT4v3Preview) в рамках программы предварительной версии.

<br>

ACU: 230-260

Хранилище класса "Премиум":  Поддерживается

Кэширование в хранилище класса Premium:  Поддерживается

Динамическая миграция: Не поддерживается

Обновления с сохранением памяти: Не поддерживается

| Размер | vCPU | Память: ГиБ | Временное хранилище (SSD): ГиБ | Графический процессор | Память GPU: ГиБ | Максимальное число дисков данных | Максимальное число сетевых адаптеров |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы

Чтобы воспользоваться возможностями GPU виртуальных машин серии NCasT4_v3 Azure под управлением Windows или Linux, необходимо установить драйверы GPU NVIDIA.

Чтобы установить драйверы NVIDIA GPU вручную, см. раздел [Установка драйвера GPU серии N для Windows](./windows/n-series-driver-setup.md) для поддерживаемых операционных систем, драйверов, установки и проверки.

## <a name="other-sizes"></a>Остальные размеры

- [Универсальные](sizes-general.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для хранилища](sizes-storage.md)
- [Оптимизированные для GPU](sizes-gpu.md)
- [Для высокопроизводительных вычислений](sizes-hpc.md)
- [Предыдущие поколения](sizes-previous-gen.md)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.
