---
title: Общие сведения о единице вычислений Azure | Документация Майкрософт
description: Обзор концепции единиц вычислений Azure. Единица вычислений Azure (ACU) предоставляет собой способ сравнения производительности ЦП для различных номеров SKU Azure.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: e344d09497a30dec546dfaedd3d78f30c7d214d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79535703"
---
# <a name="azure-compute-unit-acu"></a>Единицы вычислений Azure (ACU)

Концепция единицы вычислений Azure (ACU) позволяет сравнивать производительность ЦП разных номеров SKU Azure. Это поможет вам легко определить, какие SKU с наибольшей вероятностью удовлетворят ваши требования к производительности. На данный момент в качестве стандарта единицы ACU выбрана малая ВМ (Standard_A1), равная 100, и все прочие SKU представляют то, насколько быстрее данный SKU может выполнять стандартную нагрузку.

> [!IMPORTANT]
> Единица ACU используется только для справки. Фактические результаты для конкретной рабочей нагрузки могут отличаться.
<br>

| Семейство SKU | ACU \ виртуальные ЦП | Виртуальный процессор: ядро |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1 - A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5 - A7](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2 - A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2 - A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8 - A11](sizes-previous-gen.md) |225* | 1:1 |
| [D1 - D14](sizes-previous-gen.md) |160-250 | 1:1 |
| [D1_v2 - D15_v2](dv2-dsv2-series.md) |210–250* | 1:1 |
| [DS1 - DS14](sizes-previous-gen.md) |160-250 | 1:1 |
| [DS1_v2 - DS15_v2](dv2-dsv2-series.md) |210–250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160–190* | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160–190* | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160–190* | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160–190* | 2:1\*\*\* |
| [F2s_v2 - F72s_v2](fsv2-series.md) |195-210 * | 2:1\*\*\* |
| [F1 - F16](sizes-previous-gen.md) |210–250* | 1:1 |
| [F1s - F16s](sizes-previous-gen.md) |210–250* | 1:1 |
| [G1 - G5](sizes-previous-gen.md) |180–240* | 1:1 |
| [GS1 - GS5](sizes-previous-gen.md) |180–240* | 1:1 |
| [Высоты](h-series.md) |290–300* | 1:1 |
| [HB](hb-series.md) |199-216 * * | 1:1 |
| [HC](hc-series.md) |297-315 * | 1:1 |
| [L4s - L32s](sizes-previous-gen.md) |180–240* | 1:1 |
| [L8s_v2 - L80s_v2](lsv2-series.md) |150 - 175** | 2:1 |
| [M](m-series.md) | 160-180 | 2:1\*\*\* |

* В единицах ACU используется технология Intel® Turbo для увеличения частоты ЦП и повышения производительности.  Степень повышения производительности может различаться в зависимости от размера виртуальной машины, рабочей нагрузки и других рабочих нагрузок, выполняющихся на том же узле.
** В единицах ACU используется технология AMD® Boost для увеличения частоты ЦП и повышения производительности.  Степень повышения производительности может различаться в зависимости от размера виртуальной машины, рабочей нагрузки и других рабочих нагрузок, выполняющихся на том же узле.
*** Поддержка технологии Hyper Threading и вложенной виртуализации.

Ниже приведены ссылки на дополнительные сведения о различных размерах:

- [Общего назначения](sizes-general.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для вычислений](sizes-compute.md)
- [Оптимизированные для GPU](sizes-gpu.md)
- [Для высокопроизводительных вычислений](sizes-hpc.md)
- [Оптимизированные для хранилища](sizes-storage.md)
