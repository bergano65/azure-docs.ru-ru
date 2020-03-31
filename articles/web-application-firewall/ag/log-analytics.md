---
title: Используйте аналитику журналов Azure для изучения журналов брандмауэра Web Application Firewall
description: В этой статье показано, как можно использовать аналитику журналов Azure Log Analytics для изучения журналов брандмауэра Web Application
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/06/2019
ms.author: victorh
ms.openlocfilehash: e1bc3b58f425b374e4cae1da6e9800579e503f0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516595"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Использование Log Analytics для проверки журналов брандмауэра веб-приложения Шлюза приложений

После ввода в действие портала приложений WAF можно включить журналы для проверки того, что происходит с каждым запросом. Брандмауэр журналы дают представление о том, что WAF оценивает, сопоставляет и блокирует. С помощью журнала Analytics вы можете изучить данные внутри журналов брандмауэра, чтобы дать еще больше информации. Для получения дополнительной информации о создании рабочего пространства analytics журнала [см.](../../azure-monitor/learn/quick-create-workspace.md) Для получения дополнительной информации о запросах журналов смотрите [Обзор запросов журналов в Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Импортные журналы WAF

Чтобы импортировать журналы брандмауэра [Back-end health, diagnostic logs, and metrics for Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)в журналную аналитику, см. Если журналы брандмауэра есть в рабочем пространстве Log Analytics, вы можете просматривать данные, писать запросы, создавать визуализации и добавлять их в панель мониторинга портала.

## <a name="explore-data-with-examples"></a>Исследуйте данные с примерами

Для просмотра исходных данных в журнале брандмауэра можно запустить следующий запрос:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Это будет выглядеть как следующий запрос:

![Запрос аналитики журнала](../media/log-analytics/log-query.png)

Вы можете просверлить данные, а графики или создать визуализации отсюда. Смотрите следующие запросы в качестве отправной точки:

### <a name="matchedblocked-requests-by-ip"></a>Совмещенные/заблокированные запросы по IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Совпадающие/заблокированные запросы URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Лучшие соответствующие правила

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Топ-5 по добранных групп правил

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Добавление на панель мониторинга

После создания запроса его можно добавить в панель мониторинга.  Выберите **панель мониторинга** в правом верхнем правом верхнем ряде рабочего пространства аналитики журналов. С предыдущими четырьмя запросами, прикрепленными к примеру панели мониторинга, это данные, которые вы можете увидеть с первого взгляда:

![Панель мониторинга](../media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Дальнейшие действия

[Работоспособность бэк-энда, диагностические журналы и метрики для шлюза приложений](../../application-gateway/application-gateway-diagnostics.md)