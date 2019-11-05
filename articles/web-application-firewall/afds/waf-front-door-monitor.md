---
title: Мониторинг и ведение журнала брандмауэра веб-приложения Azure
description: Знакомство с брандмауэром веб-приложения (WAF) с мониторингом и ведением журнала FrontDoor
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 0c705139c082f13f40362e598f0fda9ba0a128a5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512487"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Мониторинг и ведение журнала брандмауэра веб-приложения Azure 

Мониторинг и ведение журнала брандмауэра веб-приложения Azure (WAF) осуществляется с помощью ведения журналов и интеграции с журналами Azure Monitor и Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

WAF с журналом FrontDoor интегрируется с [Azure Monitor](../../azure-monitor/overview.md). Azure Monitor позволяет вести мониторинг диагностических данных, включая оповещения и журналы WAF. Вы можете настроить мониторинг WAF в ресурсе передней дверцы на портале на вкладке **Диагностика** или непосредственно в службе Azure Monitor.

В портал Azure выберите тип ресурса "Передняя дверь". В области **мониторинг**/**метрик** слева можно добавить **вебаппликатионфиреваллрекуесткаунт** для отслеживания количества запросов, соответствующих правилам WAF. Настраиваемые фильтры можно создавать на основе типов действий и имен правил.

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

В следующем примере запрос получает записи Акцесслогс:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [передней дверце](../../frontdoor/front-door-overview.md).

