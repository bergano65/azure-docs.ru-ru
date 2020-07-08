---
title: Статистика задержки циклического приема в сети Azure | Документация Майкрософт
description: Сведения о статистике задержки циклического приема между регионами Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 05/26/2020
ms.author: kumud
ms.openlocfilehash: 8cf5d07bb071217a5ecafca8f351c94590291603
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83994019"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Статистика задержки циклического приема в сети Azure

Azure постоянно отслеживает задержку (скорость) основных областей своей сети с помощью внутренних средств мониторинга, а также измерений, собираемых [ThousandEyes](https://thousandeyes.com), сторонней службы искусственного мониторинга.

## <a name="how-are-the-measurements-collected"></a>Как собираются измерения?

Измерения задержки собираются от агентов ThousandEyes, размещенных в облачных регионах Azure по всему миру, которые постоянно отправляют проверки сети между собой через 1-минутные интервалы. Статистика месячной задержки определяется усреднением собранных выборок за месяц.

## <a name="april-2020-round-trip-latency-figures"></a>Показатели задержки при округлении за Апрель 2020

Ниже приведено ежемесячное среднее время приема-передачи между регионами Azure за последние 30 дней (с 30 апреля 2020). [ThousandEyes](https://thousandeyes.com)поддерживают следующие измерения.

[![Статистика задержки между регионами Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Дальнейшие шаги

Сведения о [регионах Azure](https://azure.microsoft.com/global-infrastructure/regions/).
