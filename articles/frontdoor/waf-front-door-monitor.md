---
title: Мониторинг и ведение журнала брандмауэра веб-приложения Azure
description: Знакомство с брандмауэром веб-приложения (WAF) с мониторингом и ведением журнала FrontDoor
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 675d06f3d2071022da3867a4c45137efb818980d
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849137"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Мониторинг и ведение журнала брандмауэра веб-приложения Azure 

Мониторинг и ведение журнала брандмауэра веб-приложения Azure (WAF) осуществляется с помощью ведения журналов и интеграции с журналами Azure Monitor и Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

WAF с журналом FrontDoor интегрируется с [Azure Monitor](../azure-monitor/overview.md). Azure Monitor позволяет вести мониторинг диагностических данных, включая оповещения и журналы WAF. Вы можете настроить мониторинг WAF в ресурсе передней дверцы на портале на вкладке **Диагностика** или непосредственно в службе Azure Monitor.

В портал Azure выберите тип ресурса "Передняя дверь". На вкладке метрики **мониторинга**/слева можно добавить **вебаппликатионфиреваллрекуесткаунт** для отслеживания количества запросов, соответствующих правилам WAF. Настраиваемые фильтры можно создавать на основе типов действий и имен правил.

![вафметрикс](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Журналы и диагностика

WAF с передней дверцей предоставляет подробные отчеты по каждой обнаруженной угрозе. Функция ведения журналов интегрирована с журналами системы диагностики Azure, и оповещения записываются в формате JSON. Эти журналы можно интегрировать с [журналами Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![вафдиаг](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

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

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о [передней дверце](front-door-overview.md).

