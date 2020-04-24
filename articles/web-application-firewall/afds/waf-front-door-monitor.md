---
title: Мониторинг и ведение журнала брандмауэра веб-приложения Azure
description: Знакомство с брандмауэром веб-приложения (WAF) с мониторингом и ведением журнала FrontDoor
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: b4f666415a96307b89022c6caf6af90581f294f3
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115369"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Мониторинг и ведение журнала брандмауэра веб-приложения Azure 

Мониторинг и ведение журнала брандмауэра веб-приложения Azure (WAF) осуществляется с помощью ведения журналов и интеграции с журналами Azure Monitor и Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

WAF с журналом FrontDoor интегрируется с [Azure Monitor](../../azure-monitor/overview.md). Azure Monitor позволяет вести мониторинг диагностических данных, включая оповещения и журналы WAF. Вы можете настроить мониторинг WAF в ресурсе передней дверцы на портале на вкладке **Диагностика** или непосредственно в службе Azure Monitor.

В портал Azure выберите тип ресурса "Передняя дверь". На вкладке**метрики** **мониторинга**/слева можно добавить **вебаппликатионфиреваллрекуесткаунт** для отслеживания количества запросов, соответствующих правилам WAF. Настраиваемые фильтры можно создавать на основе типов действий и имен правил.

![вафметрикс](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Журналы и диагностика

WAF с передней дверцей предоставляет подробные отчеты по каждой обнаруженной угрозе. Функция ведения журналов интегрирована с журналами системы диагностики Azure, и оповещения записываются в формате JSON. Эти журналы можно интегрировать с [журналами Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![вафдиаг](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

Фронтдуракцесслог регистрирует все запросы, перенаправляемые на серверные концы. Фронтдурвебаппликатионфиревалллог регистрирует любой запрос, соответствующий правилу WAF.

В следующем примере запрос получает журналы WAF для заблокированных запросов:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Ниже приведен пример зарегистрированного запроса в журнале WAF:

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

В следующем примере запрос получает записи Акцесслогс:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Ниже приведен пример зарегистрированного запроса в журнале доступа:

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

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о [передней дверце](../../frontdoor/front-door-overview.md).
