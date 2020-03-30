---
title: Статистика задержки сети Azure в оба конца Документы Майкрософт
description: Узнайте о статистике задержки в оба конца между регионами Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: 3469daa8b81b20d5d0052a23ce3236fc4fde75de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082950"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Статистика задержки сети Azure

Azure постоянно отслеживает задержку (скорость) основных областей своей сети с помощью инструментов внутреннего мониторинга, а также измерения, собранные [ThousandEyes,](https://thousandeyes.com)сторонней службой синтетического мониторинга.

## <a name="how-are-the-measurements-collected"></a>Как собираются измерения?

Измерения задержки собираются с помощью агентов ThousandEyes, размещенных в облачных регионах Azure по всему миру, которые непрерывно отправляют сетевые зонды между собой через 1-минутные интервалы. Ежемесячные статистические данные о задержках получены на основе усреднения собранных образцов за месяц.

## <a name="february-2020-round-trip-latency-figures"></a>Февраль 2020 в оба конца данные задержки

Ниже приведено среднемесячное время в пути между регионами Azure за последние 29 дней (окончание 29 февраля 2020 года). Следующие измерения питаются от [ThousandEyes](https://thousandeyes.com).

[![Статистика межрайонных задержк Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о [регионах Azure](https://azure.microsoft.com/global-infrastructure/regions/).
