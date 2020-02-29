---
title: Виртуальные машины Azure серии HC
description: Спецификации виртуальных машин серии HC.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: cc25fb9b21d535ef07bcfae673be48216427b370
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164786"
---
# <a name="hc-series"></a>Серия HC

Виртуальные машины серии HC оптимизированы для приложений, управляемых несжатыми вычислениями, например неявным анализом конечных элементов, молекулярное Dynamics и вычислительными химия. HC VMS 44 процессорных ядер Intel Xeon Platinum 8168, 8 ГБ ОЗУ на ядро ЦП и без технологии hypering. Платформа Intel Xeon Platinum поддерживает обширную экосистему программных средств Intel, например библиотеку Intel Math Kernel.

ACU: 297-315

Хранилище класса Premium: поддерживается

Кэширование в хранилище класса Premium: поддерживается

Динамическая миграция: не поддерживается

Обновления с сохранением памяти: не поддерживается

| Размер | vCPU | Процессор | Память (ГБ) | Пропускная способность памяти ГБ/с | Базовая частота ЦП (ГГц) | Частота ядер (ГГц, пик) | Частота с одним ядром (ГГц, пик) | Производительность RDMA (ГБ/с) | Поддержка MPI | Хранилище Temp (ГБ) | Максимальное число дисков данных | Максимальное число сетевых адаптеров Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3,7 | 100 | Все | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Остальные размеры

- [Универсальные](sizes-general.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для хранилища](sizes-storage.md)
- [Оптимизированные для GPU](sizes-gpu.md)
- [Для высокопроизводительных вычислений](sizes-hpc.md)
- [Предыдущие поколения](sizes-previous-gen.md)

## <a name="next-steps"></a>Следующие шаги

Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.