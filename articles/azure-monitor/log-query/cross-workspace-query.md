---
title: Запрос ресурсов с помощью Azure Monitor | Документация Майкрософт
description: Из этой статьи вы узнаете, как выполнять запросы к ресурсам из нескольких рабочих областей и приложения App Insights в вашей подписке.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: magoedte
ms.openlocfilehash: b0d12021be5a5dca348ea3ffa3f0b853725812da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589302"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Выполнение запросов журнала между ресурсами в Azure Monitor  

Ранее с помощью службы Azure Monitor вы могли анализировать данные только в пределах текущей рабочей области. Это препятствовало выполнению запросов в нескольких рабочих областях, определенных вашей подпиской.  Кроме того, элементы телеметрии, полученные из веб-приложения с помощью Application Insights, можно было искать только непосредственно в Application Insights или из Visual Studio.  Это также усложняло встроенный совместный анализ операционных данных и данных приложения.   

Теперь вы можете выполнять запросы не только в нескольких рабочих областях Log Analytics, но также запрашивать данные из приложения Application Insights в той же или другой группе ресурсов или в другой подписке. Благодаря этому вы можете получить представление данных на уровне системы.  Запросы таких типов можно выполнять только в [Log Analytics](portals.md).

## <a name="cross-resource-query-limits"></a>Ограничения запросов между ресурсами 

* Количество ресурсов Application Insights, которые могут включать в одном запросе ограничено до 100.
* Запрос нескольких ресурсов не поддерживается в конструкторе представлений. Можно создать запрос в Log Analytics и закрепить на панели мониторинга Azure и [визуализировать поиск по журналам](../../azure-monitor/learn/tutorial-logs-dashboards.md#visualize-a-log-search). 
* Запросы между ресурсами в оповещения журнала поддерживаются в новом [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). По умолчанию Azure Monitor использует [устаревшие API оповещения Log Analytics](../platform/api-alerts.md) для создания любого нового правила генерации оповещений на портале Azure, пока вы не переключаетесь с [устаревших API оповещений журнала](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). После переключения новый API используется по умолчанию для новых правил генерации оповещений на портале Azure и позволяет создавать правила генерации оповещений журнала запроса между разными ресурсами. Журнал запросов между ресурсами можно создать правила генерации оповещений не переключаетесь с помощью [шаблон ARM для scheduledQueryRules API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – но этого правила генерации оповещений можно управлять, хотя [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) а не из портала Azure.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Выполнение запросов в рабочих областях Log Analytics и запрос данных из приложения Application Insights
Воспользуйтесь идентификатором [*рабочей области*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) для обращения к другой рабочей области в запросе, а для Application Insights используйте идентификатор [*приложения*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression).  

### <a name="identifying-workspace-resources"></a>Определение ресурсов рабочей области
В приведенных ниже примерах демонстрируются запросы к различным рабочим областям Log Analytics. Эти запросы предназначены для получения итогового количества журналов из таблицы "Обновление" в рабочей области с именем *contosoretail-it*. 

Определить рабочую область можно несколькими способами:

* Имя ресурса — это имя рабочей области в удобном для восприятия формате, которое иногда называется *именем компонента*. 

    `workspace("contosoretail-it").Update | count`
 
    >[!NOTE]
    >При определении рабочей области по имени предполагается его уникальность во всех доступных подписках. Если у вас есть несколько приложений с таким именем, запрос не выполнится из-за неоднозначности. В этом случае необходимо воспользоваться другим идентификатором.

* Полное имя — это имя рабочей области, состоящее из имени подписки, группы ресурсов и компонента в следующем формате: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Так как имена подписки Azure не являются уникальными, этот идентификатор может быть неоднозначным. 
    >

* Идентификатор рабочей области — это уникальный, неизменяемый идентификатор, присвоенный каждой рабочей области и представленный в виде глобального уникального идентификатора (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Идентификатор ресурса Azure — это уникальный идентификатор рабочей области в Azure. Он используется, если имя ресурса является неоднозначным.  Для рабочих областей используется следующий формат: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName*.  

    Например: 
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Определение приложения
В приведенных ниже примерах возвращается итоговое количество запросов к приложению *fabrikamapp* в Application Insights. 

Определение приложения в Application Insights можно обеспечить с помощью выражения *app(Identifier)*.  Аргумент *Identifier* указывает приложение с помощью одного из следующих объектов:

* Имя ресурса — это имя приложения в удобном для восприятия формате, которое иногда называется *именем компонента*.  

    `app("fabrikamapp")`

* Полное имя — имя приложения, состоящее из имени подписки, группы ресурсов и компонента в следующем формате: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Так как имена подписки Azure не являются уникальными, этот идентификатор может быть неоднозначным. 
    >

* Идентификатор — глобальный уникальный идентификатор приложения.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Идентификатор ресурса Azure — это уникальный идентификатор приложения в Azure. Он используется, если имя ресурса является неоднозначным. Используется такой формат: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. Компоненты/OperationalInsights/componentName*.  

    Например: 
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Выполнение запроса для нескольких ресурсов
Вы можете запрашивать несколько ресурсов из любого экземпляра ресурса: это может быть сочетание рабочих областей и приложений.
    
Пример запроса в двух рабочих областях:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Использование запроса между ресурсами для нескольких ресурсов
Если использовать запросы между ресурсами, чтобы коррелировать данные из нескольких рабочих областей Log Analytics и ресурсов Application Insights, это усложнит запрос и его будет трудно обработать. Поэтому следует использовать [функции в запросах журнала Azure Monitor](functions.md), чтобы отделить логику запроса от его области ресурсов, благодаря чему структура запроса становится упрощенной. В приведенном ниже примере показано, как отслеживать несколько ресурсов Application Insights и визуализировать количество неудачных запросов по имени приложения. 

Создайте запрос, как показано ниже, который ссылается на область ресурсов Application Insights. Команда `withsource= SourceApp` позволяет добавить столбец, который определяет имя приложения, отправившего журнал. [Сохраните запрос как функцию](functions.md#create-a-function) с псевдонимом _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Теперь вы можете [использовать эту функцию](../../azure-monitor/log-query/functions.md#use-a-function) в запросе между ресурсами, как показано ниже. Псевдоним функции _applicationsScoping_ возвращает объединение таблицы запросов из всех определенных приложений. После этого с помощью запроса фильтруются неудачные запросы и визуализируются тенденции по приложению. Оператор _parse_ в этом примере не является обязательным. Оператор позволяет извлечь имя приложения из свойства _SourceApp_.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```
![Timechart](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите статью [Анализ данных журнала в Azure Monitor](log-query-overview.md), где описываются запросы журнала и структуры данных журнала Azure Monitor.
- Сведения о всех ресурсах см. в статье [Azure Monitor log queries](query-language.md) (Запросы журнала Azure Monitor).
