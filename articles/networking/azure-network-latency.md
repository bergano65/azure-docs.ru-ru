---
title: Статистика задержки приема-передачи для сети Azure | Документация Майкрософт
description: Сведения о статистике задержки приема-передачи между регионами Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 08/05/2020
ms.author: kumud
ms.openlocfilehash: 72168a56bfb4e08c7f44c84c773d9f6599cfa607
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91848894"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Статистика задержки приема-передачи для сети Azure

Azure постоянно отслеживает задержку (скорость) основных областей своей сети с помощью внутренних средств мониторинга, а также измерений, собираемых [ThousandEyes](https://thousandeyes.com), сторонней службы искусственного мониторинга.

## <a name="how-are-the-measurements-collected"></a>Как собираются измерения?

Измерения задержки собираются от агентов ThousandEyes, размещенных в облачных регионах Azure по всему миру, которые постоянно отправляют проверки сети между собой через 1-минутные интервалы. Статистика месячной задержки определяется усреднением собранных выборок за месяц.

## <a name="september-2020-round-trip-latency-figures"></a>Показатели задержки при округлении 2020 сентября

Ниже приведено ежемесячное среднее время приема-передачи между регионами Azure за последние 30 дней (до 30 сентября 2020). [ThousandEyes](https://thousandeyes.com)поддерживают следующие измерения.

[![Статистика задержки между регионами Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

Сведения о [регионах Azure](https://azure.microsoft.com/global-infrastructure/regions/).
