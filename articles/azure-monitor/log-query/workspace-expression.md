---
title: Выражение workspace() в запросе журнала Azure Monitor | Документация Майкрософт
description: Выражение workspace используется в запросе журнала Azure Monitor для получения данных из определенной рабочей области в той же или другой группе ресурсов или в другой подписке.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: 933d37f576d0b8507d2311a3e31e34182a0a2e69
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269841"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>Выражение workspace() в запросе журнала Azure Monitor

Выражение `workspace` используется в запросе Azure Monitor для получения данных из определенной рабочей области в той же или другой группе ресурсов или в другой подписке. Оно используется для включения данных журнала в запрос Application Insights и получения данных из нескольких рабочих областей.


## <a name="syntax"></a>Синтаксис

`workspace(`*Идентификатор*`)`

## <a name="arguments"></a>Аргументы

- *Идентификатор:* определяет рабочую область с помощью одного из форматов в следующей таблице.

| Идентификатор | ОПИСАНИЕ | Пример
|:---|:---|:---|
| Имя ресурса | Понятное имя рабочей области (имя компонента). | workspace("contosoretail") |
| Полное имя | Полное имя рабочей области в формате: subscriptionName/resourceGroup/componentName. | workspace('Contoso/ContosoResource/ContosoWorkspace') |
| ИД | Идентификатор GUID рабочей области. | workspace("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| Идентификатор ресурса Azure | Идентификатор ресурса Azure. | workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Примечания

* У вас должен быть доступ на чтение к рабочей области.
* Связанное выражение — `app`. Оно позволяет выполнять запросы к приложениям Application Insights.

## <a name="examples"></a>Примеры

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения см. в статье [Выражение app() в запросах Log Analytics](app-expression.md).
- Подробнее о хранении данных Azure Monitor см. в статье [Анализ данных Log Analytics в Azure Monitor](log-query-overview.md).
- Ознакомьтесь с полной документацией по [языку запросов Kusto](/azure/kusto/query/).