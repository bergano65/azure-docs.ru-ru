---
title: Статистика задержки циклического приема в сети Azure | Документация Майкрософт
description: Сведения о статистике задержки циклического приема между регионами Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: 3469daa8b81b20d5d0052a23ce3236fc4fde75de
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082950"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Статистика задержки циклического приема в сети Azure

Azure постоянно отслеживает задержку (скорость) основных областей своей сети с помощью внутренних средств мониторинга, а также измерений, собираемых [ThousandEyes](https://thousandeyes.com), сторонней службы искусственного мониторинга.

## <a name="how-are-the-measurements-collected"></a>Как собираются измерения?

Измерения задержки собираются от агентов ThousandEyes, размещенных в облачных регионах Azure по всему миру, которые постоянно отправляют проверки сети между собой через 1-минутные интервалы. Статистика месячной задержки определяется усреднением собранных выборок за месяц.

## <a name="february-2020-round-trip-latency-figures"></a>Данные о задержке кругового пути 2020 февраля

Ниже приведено ежемесячное среднее время приема-передачи между регионами Azure за последние 29 дней (заканчивается 29 февраля 2020). [ThousandEyes](https://thousandeyes.com)поддерживают следующие измерения.

[![статистики задержки между регионами Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

Сведения о [регионах Azure](https://azure.microsoft.com/global-infrastructure/regions/).
