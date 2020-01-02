---
title: Модель данных телеметрии исключений Application Insights Azure
description: Модель данных Application Insights для телеметрии исключений
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 7decac2e5e65ff7eedb374858fe15360b4a1b2c5
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872627"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Телеметрия исключений: модель данных Application Insights

В [Application Insights](../../azure-monitor/app/app-insights-overview.md) экземпляр исключения представляет обработанное или необработанное исключение, возникшее во время выполнения отслеживаемого приложения.

## <a name="problem-id"></a>Идентификатор проблемы

Идентификатор места в коде, где возникло исключение. Используется для группирования исключений. Обычно это сочетание типа исключения и функции из стека вызовов.

Максимальная длина: 1024 символа

## <a name="severity-level"></a>Уровень серьезности

Уровень серьезности трассировки. Возможные значения: `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Сведения об исключении

(Эти сведения будут добавлены позже.)

## <a name="custom-properties"></a>Пользовательские свойства

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Пользовательские измерения

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Дальнейшие действия

- В [этой статье](data-model.md) представлены типы данных и модель данных для Application Insights.
- Вы можете научиться [диагностировать исключения в веб-приложениях с помощью Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Ознакомление с [платформами](../../azure-monitor/app/platforms.md), поддерживаемыми Application Insights.
