---
title: Статистика задержки приема-передачи для сети Azure | Документация Майкрософт
description: Сведения о статистике задержки приема-передачи между регионами Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 08/05/2020
ms.author: kumud
ms.openlocfilehash: 1898bcf619f1fc9aaf19384968225e35c9589688
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87847450"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Статистика задержки приема-передачи для сети Azure

Azure постоянно отслеживает задержку (скорость) основных областей своей сети с помощью внутренних средств мониторинга, а также измерений, собираемых [ThousandEyes](https://thousandeyes.com), сторонней службы искусственного мониторинга.

## <a name="how-are-the-measurements-collected"></a>Как собираются измерения?

Измерения задержки собираются от агентов ThousandEyes, размещенных в облачных регионах Azure по всему миру, которые постоянно отправляют проверки сети между собой через 1-минутные интервалы. Статистика месячной задержки определяется усреднением собранных выборок за месяц.

## <a name="july-2020-round-trip-latency-figures"></a>Цифры задержки приема-передачи (Июль 2020)

Ниже приведено ежемесячное среднее время приема-передачи между регионами Azure за последние 31 день (заканчивается 31 июля 2020). [ThousandEyes](https://thousandeyes.com)поддерживают следующие измерения.

[![Статистика задержки между регионами Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Дальнейшие шаги

Сведения о [регионах Azure](https://azure.microsoft.com/global-infrastructure/regions/).
