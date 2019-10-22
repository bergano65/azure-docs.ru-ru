---
title: Модель данных телеметрии Azure Application Insights — телеметрия трассировки | Документы Майкрософт
description: Модель данных Application Insights для телеметрии трассировки
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 855a93d8a6350c625fe0820fae83644aec3459ed
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678112"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Телеметрия трассировки: модель данных Application Insights

Телеметрия трассировки (в [Application Insights](../../azure-monitor/app/app-insights-overview.md)) представляет инструкции трассировки стиля `printf`, доступные для текстового поиска. `Log4Net`, `NLog` и другие записи файла журнала на основе текста преобразуются в экземпляры этого типа. Трассировка не имеет измерений в качестве расширения.

## <a name="message"></a>Сообщение

Сообщение трассировки.

Максимальная длина: 32 768 символов

## <a name="severity-level"></a>Уровень серьезности

Уровень серьезности трассировки. Возможные значения: `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Пользовательские свойства

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Дальнейшие действия

- [Просмотр журналов трассировки .NET в Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Просмотр журналов трассировки Java в Application Insights](../../azure-monitor/app/java-trace-logs.md).
- В [этой статье](data-model.md) представлены типы данных и модель данных для Application Insights.
- [Написание пользовательской телеметрии трассировки](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Ознакомление с [платформами](../../azure-monitor/app/platforms.md), поддерживаемыми Application Insights.
