---
title: Использование Azure Log Analytics для просмотра журналов брандмауэра веб-приложения для шлюза приложений
description: В этой статье показано, как можно использовать Azure Log Analytics для просмотра журналов брандмауэра веб-приложения для шлюза приложений
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: aa867e33ef0faa96b6a66a9075a3a5b8b0b0bca4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712180"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Использовать Log Analytics для анализа приложения шлюза ведение журналов

После вашего WAF шлюза приложений работает, вы можете включить журналы, чтобы проверить, что происходит с каждым запросом. Брандмауэр журналы позволяют получить представление о какие WAF оценки, сопоставления и блокировки. С помощью Log Analytics можно просмотреть данные в журналы брандмауэра, которые будут содержать даже больше информации. Дополнительные сведения о создании рабочей области Log Analytics, см. в разделе [создать рабочую область Log Analytics на портале Azure](../azure-monitor/learn/quick-create-workspace.md). Дополнительные сведения о журнале запросов, см. в разделе [Общие сведения о журнале запросов в Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Журналы WAF импорта

Чтобы импортировать журналы брандмауэра в Log Analytics, см. в разделе [работоспособности серверной части, журналы диагностики и метрики для шлюза приложений](application-gateway-diagnostics.md#diagnostic-logging). При наличии журналы брандмауэра в рабочей области Log Analytics, можно просматривать данные, создавать запросы, создания визуализаций и добавить их к панели мониторинга портала.

## <a name="explore-data-with-examples"></a>Просмотр данных с примерами

Чтобы просмотреть необработанные данные в журнале брандмауэра, необходимо выполнить следующий запрос:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Это будет выглядеть примерно следующий запрос:

![Запрос log Analytics](media/log-analytics/log-query.png)

Можно детализировать данные и построения диаграмм или создавать визуализации отсюда. См. следующие запросы в качестве отправной точки:

### <a name="matchedblocked-requests-by-ip"></a>Соответствие или заблокированных запросов по IP-Адресу

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Соответствие или заблокированных запросов по URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>TOP сопоставляемых правил

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Первые пять групп из этих правил

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Добавить на панель мониторинга

После создания запроса, его можно добавить на панель мониторинга.  Выберите **закрепить на панели мониторинга** в правом верхнем углу рабочей области log analytics. С помощью предыдущих четырех запросов, закрепленные на пример панели мониторинга это данные, которые можно увидеть с первого взгляда:

![панель мониторинга](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Следующие шаги

[Работоспособность серверной части, журналы диагностики и метрики для шлюза приложений](application-gateway-diagnostics.md)
