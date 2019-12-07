---
title: Статистика задержки приема-передачи для сети Azure | Документация Майкрософт
description: Сведения о статистике задержки приема-передачи между регионами Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 12/04/2019
ms.author: mnayak
ms.openlocfilehash: 3947df81b67d5aefc1b628b6ddaf8275152a4cd3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893097"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Статистика задержки приема-передачи для сети Azure

Azure постоянно отслеживает задержку (скорость) основных областей своей сети с помощью внутренних средств мониторинга, а также измерений, собираемых [ThousandEyes](https://thousandeyes.com), сторонней службы искусственного мониторинга.

## <a name="how-are-the-measurements-collected"></a>Как собираются измерения?

Измерения задержки собираются от агентов ThousandEyes, размещенных в облачных регионах Azure по всему миру, которые постоянно отправляют проверки сети между собой через 1-минутные интервалы. Статистика месячной задержки определяется усреднением собранных выборок за месяц.

## <a name="november-2019-latency-figures"></a>Показатели задержки 2019 ноября

**Ноябрьское обновление:** Добавлено 3 региона.

* Восточная Норвегия
* Западная Норвегия
* Австралия

Ниже приведено ежемесячное среднее время приема-передачи между регионами Azure за последние 30 дней (до 30 ноября 2019). [ThousandEyes](https://thousandeyes.com)поддерживают следующие измерения.

![Статистика задержки между регионами Azure](media/azure-network-latency/latency-nov-2019.png)

## <a name="next-steps"></a>Дальнейшие действия

Сведения о [регионах Azure](https://azure.microsoft.com/global-infrastructure/regions/).
