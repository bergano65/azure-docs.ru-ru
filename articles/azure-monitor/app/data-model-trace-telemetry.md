---
title: Модель данных Application Insights Azure — данные телеметрии трассировки
description: Модель данных Application Insights для телеметрии трассировки
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 31958b26cdb8a7897cf0051af6600014c07949fd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671959"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Телеметрия трассировки: модель данных Application Insights

Телеметрия трассировки (в [Application Insights](../../azure-monitor/app/app-insights-overview.md)) представляет инструкции трассировки стиля `printf`, доступные для текстового поиска. `Log4Net`, `NLog` и другие записи файла журнала на основе текста преобразуются в экземпляры этого типа. Трассировка не имеет измерений в качестве расширения.

## <a name="message"></a>Сообщение

Сообщение трассировки.

Максимальная длина: 32 768 символов

## <a name="severity-level"></a>Степень серьезности

Уровень серьезности трассировки. Возможные значения: `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Пользовательские свойства

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Дальнейшие шаги

- [Просмотр журналов трассировки .NET в Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Изучите журналы трассировки Java в Application Insights](../../azure-monitor/app/java-trace-logs.md).
- В [этой статье](data-model.md) представлены типы данных и модель данных для Application Insights.
- [Написание пользовательской телеметрии трассировки](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Ознакомление с [платформами](../../azure-monitor/app/platforms.md), поддерживаемыми Application Insights.
