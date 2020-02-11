---
title: Статистика задержки циклического приема в сети Azure | Документация Майкрософт
description: Сведения о статистике задержки циклического приема между регионами Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 02/10/2020
ms.author: kumud
ms.openlocfilehash: dc2cb6545093e8ab70d18014a5fd9d0e7d36f8b7
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122395"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Статистика задержки циклического приема в сети Azure

Azure постоянно отслеживает задержку (скорость) основных областей своей сети с помощью внутренних средств мониторинга, а также измерений, собираемых [ThousandEyes](https://thousandeyes.com), сторонней службы искусственного мониторинга.

## <a name="how-are-the-measurements-collected"></a>Как собираются измерения?

Измерения задержки собираются от агентов ThousandEyes, размещенных в облачных регионах Azure по всему миру, которые постоянно отправляют проверки сети между собой через 1-минутные интервалы. Статистика месячной задержки определяется усреднением собранных выборок за месяц.

## <a name="january-2020-round-trip-latency-figures"></a>Цифры задержки при круговой задержке 2020 января

Ниже приведено ежемесячное среднее время приема-передачи между регионами Azure за последние 31 день (начиная с 31 января 2020). [ThousandEyes](https://thousandeyes.com)поддерживают следующие измерения.

[![статистики задержки между регионами Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

Сведения о [регионах Azure](https://azure.microsoft.com/global-infrastructure/regions/).
