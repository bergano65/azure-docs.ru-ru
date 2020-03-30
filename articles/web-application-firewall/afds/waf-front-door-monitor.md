---
title: Мониторинг и журнал ирование веб-приложений Azure Web Application
description: Изучите веб-приложение Firewall (WAF) с frontDoor мониторинга и регистрации
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 4488fadf5db3b32049b5dce4bbee1fa76c320e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284149"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Мониторинг и журнал ирование веб-приложений Azure Web Application 

Мониторинг и журналирование веб-приложений Azure Web Application Firewall (WAF) обеспечивается путем регистрации и интеграции с журналами Azure Monitor и Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

WAF с журналом FrontDoor интегрирован с [Azure Monitor.](../../azure-monitor/overview.md) Azure Monitor позволяет отслеживать диагностическую информацию, включая оповещения WAF и журналы. Вы можете настроить мониторинг WAF в ресурсе Front Door на портале под вкладкой **Диагностика** или через службу Azure Monitor напрямую.

С портала Azure перейдите к типу ресурсов Front Door. С**вкладки «Метрика** **мониторинга»**/слева можно добавить **WebApplicationFirewallRequestCount** для отслеживания количества запросов, которые соответствуют правилам WAF. Пользовательские фильтры могут создаваться на основе типов действий и имен правил.

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Логи и диагностика

WAF с Front Door предоставляет подробную отчетность о каждой обнаруженной угрозе. Функция ведения журналов интегрирована с журналами системы диагностики Azure, и оповещения записываются в формате JSON. Эти журналы можно интегрировать с [журналами Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog регистрирует все запросы, которые направляются клиенту. FrontdoorWebApplicationFirewallLog регистрирует любой запрос, который соответствует правилу WAF.

Следующий пример запроса получает журналы WAF на заблокированных запросах:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_name_s == "Block"

```

Вот пример запроса в журнале WAF:

``` WAFlogQuerySample
{
    "PreciseTimeStamp": "2020-01-25T00:11:19.3866091Z",
    "time": "2020-01-25T00:11:19.3866091Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "xx.xx.xxx.xxx",
        "socketIP": "xx.xx.xxx.xxx",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/?q=../../x",
        "ruleName": "Microsoft_DefaultRuleSet-1.1-LFI-930100",
        "policy": "WafDemoCustomPolicy",
        "action": "Block",
        "host": "wafdemofrontdoorwebapp.azurefd.net",
        "refString": "0p4crXgAAAABgMq5aIpu0T6AUfCYOroltV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode": "prevention"
    }
}

``` 

Следующий пример запроса получает записи AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Вот пример зарегистрированного запроса в журнале Access:

``` AccessLogSample
{
    "PreciseTimeStamp": "2020-01-25T00:11:12.0160150Z",
    "time": "2020-01-25T00:11:12.0160150Z",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0n4crXgAAAACnRKbdALbyToAqNfSHssDvV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "httpMethod": "GET",
        "httpVersion": "2.0",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/",
        "requestBytes": "710",
        "responseBytes": "3116",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4017.0 Safari/537.36 Edg/81.0.389.2",
        "clientIp": "xx.xx.xxx.xxx",
        "timeTaken": "0.598",
        "securityProtocol": "TLS 1.2",
        "routingRuleName": "WAFdemoWebAppRouting",
        "backendHostname": "wafdemouksouth.azurewebsites.net:443",
        "sentToOriginShield": false,
        "httpStatusCode": "200",
        "httpStatusDetails": "200"
    }
}

```

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [передней двери](../../frontdoor/front-door-overview.md).
