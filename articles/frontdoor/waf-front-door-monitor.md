---
title: Брандмауэр Azure веб-приложения мониторинга и ведения журнала
description: Узнайте с FrontDoor мониторинг и ведение журнала брандмауэра веб-приложения (WAF)
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: e4ba6cca679ce4910ea941d9578939721514b2ec
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478968"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Брандмауэр Azure веб-приложения мониторинга и ведения журнала 

Azure web application брандмауэра (WAF) мониторинг и ведение журнала обеспечивается ведения журналов и интеграции с Azure Monitor и Azure Monitor журналы.

## <a name="azure-monitor"></a>Azure Monitor

WAF с журналом FrontDoor интегрирован с [Azure Monitor](../azure-monitor/overview.md). Azure Monitor позволяет отслеживать диагностические сведения, включая журналы и оповещения WAF. Вы можете настроить мониторинг WAF двери ресурса на портале в разделе **диагностики** вкладку или с помощью Azure Monitor службы напрямую.

На портале Azure перейдите к двери тип ресурса. Из **мониторинг**/**метрики** вкладке слева, можно добавить **WebApplicationFirewallRequestCount** для отслеживания количества запросов, соответствующих правил WAF. Настраиваемые фильтры могут создаваться на основе типов действий и имена правил.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Журналы и диагностика

WAF с дверью предоставляет подробные отчеты о каждой из обнаруженных угроз. Функция ведения журналов интегрирована с журналами системы диагностики Azure, и оповещения записываются в формате JSON. Эти журналы можно интегрировать с [журналами Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog регистрирует все запросы, которые пересылаются серверные решения клиента. FrontdoorWebApplicationFirewallLog заносит в журнал любой запрос, который соответствует правилу WAF.

Следующий запрос получает журналы WAF на заблокированных запросов:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Следующий запрос получает записи AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [двери](front-door-overview.md).

