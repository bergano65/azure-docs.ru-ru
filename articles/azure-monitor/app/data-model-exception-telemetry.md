---
title: Модель данных телеметрии Azure Application Insights — телеметрия исключений | Документы Майкрософт
description: Модель данных Application Insights для телеметрии исключений
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: efd7ad43ee9a2206f474621612eca7dfe5079f99
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117102"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Телеметрия исключений. Модель данных Application Insights

В [Application Insights](../../azure-monitor/app/app-insights-overview.md) экземпляр исключения представляет обработанное или необработанное исключение, возникшее во время выполнения отслеживаемого приложения.

## <a name="problem-id"></a>Идентификатор проблемы

Идентификатор места в коде, где возникло исключение. Используется для группирования исключений. Обычно это сочетание типа исключения и функции из стека вызовов.

Максимальная длина — 1024 символа

## <a name="severity-level"></a>Уровень серьезности

Уровень серьезности трассировки. Возможные значения: `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Сведения об исключении

(Эти сведения будут добавлены позже.)

## <a name="custom-properties"></a>Пользовательские свойства

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Пользовательские измерения

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Дополнительная информация

- В [этой статье](data-model.md) представлены типы данных и модель данных для Application Insights.
- Вы можете научиться [диагностировать исключения в веб-приложениях с помощью Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Ознакомление с [платформами](../../azure-monitor/app/platforms.md), поддерживаемыми Application Insights.
