---
title: Статистика задержки приема-передачи для сети Azure | Документация Майкрософт
description: Сведения о статистике задержки приема-передачи между регионами Azure.
services: networking
documentationcenter: na
author: nayak-mahesh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/04/2019
ms.author: mnayak
ms.openlocfilehash: 500676983233f943fdc9638d75758645dee65564
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587593"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Статистика задержки приема-передачи для сети Azure

Azure постоянно отслеживает задержку (скорость) основных областей своей сети с помощью внутренних средств мониторинга, а также измерений, собираемых [ThousandEyes](https://thousandeyes.com), сторонней службы искусственного мониторинга.

## <a name="how-are-the-measurements-collected"></a>Как собираются измерения?

Измерения задержки собираются от ThousandEyes агентов, размещенных в облачных регионах Azure, которые постоянно отправляют сетевые проверки между собой через 1 минуту. Статистика месячной задержки определяется усреднением собранных выборок за месяц.

## <a name="october-2019-latency-figures"></a>Показатели задержки 2019 октября

В течение 31 дня, начиная с 31 октября 2019 г., месячная задержка и максимальное время задержки кругового пути в агрегированных регионах:

- от **5 мс** до **72 мс** для круговых поездок в **Северная Америка** регионах.
- **3 МС** до **28 МС** для круговых поездок в странах **Европы** .
- от **4** до **134 МС** для круговых поездок в регионах **Азии** .

[ThousandEyes](https://thousandeyes.com)поддерживают следующие измерения задержки между регионами. Единица измерения в таблице ниже измеряется в миллисекундах (МС).

![Статистика задержки между регионами Azure](media/azure-network-latency/azure-inter-region-latency.png)


## <a name="next-steps"></a>Дальнейшие действия
- Сведения о [регионах Azure](https://azure.microsoft.com/global-infrastructure/regions/).