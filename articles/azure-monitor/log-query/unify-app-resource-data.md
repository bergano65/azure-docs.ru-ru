---
title: Объединение нескольких ресурсов Azure Monitor Application Insights | Документация Майкрософт
description: В этой статье содержатся сведения по использованию функции журналов Azure Monitor для запроса нескольких ресурсов Application Insights и визуализации данных.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: magoedte
ms.openlocfilehash: 3f3de81197b05d4f025a3fd8638cffe4b07cecad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61424659"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Объединение нескольких ресурсов Azure Monitor Application Insights 
В этой статье описывается выполнение запросов и централизованный просмотр всех данных журнала приложения Application Insights (в качестве замены устаревающему Соединителю Application Insights), даже если они находятся в разных подписках Azure. Вы можете включить в один запрос не более 100 ресурсов Application Insights.  

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Рекомендуемый подход для запроса нескольких ресурсов Application Insights 
Перечисление нескольких ресурсов Application Insights в запросе может быть весьма трудоемким и трудным в поддержке процессом. Вместо этого вы можете использовать функции для отделения логики запросов и области приложений.  

В примере ниже показано, как отслеживать несколько ресурсов Application Insights и визуализировать количество неудачных запросов по имени приложения. Перед началом работы выполните этот запрос в рабочей области, которая подключена к ресурсам Application Insights, чтобы получить список подключенных приложений: 

```
ApplicationInsights
| summarize by ApplicationName
```

Создайте функцию с помощью оператора union со списком приложений, затем сохраните запрос в рабочей области в качестве функции с псевдонимом *applicationsScoping*.  

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

>[!NOTE]
>Вы можете в любое время изменить список приложений на портале, перейдя к обозревателю запросов в рабочей области и выбрав функцию для редактирования с последующим сохранением, или с помощью командлета PowerShell `SavedSearch`. Команда `withsource= SourceApp` позволяет добавить в результаты столбец, который обозначает приложение, отправившее журнал. 
>
>Запрос использует схему Application Insights, несмотря на то что запрос выполняется в рабочей области, так как функция applicationsScoping возвращает структуру данных Application Insights. 
>
>Оператор parse является необязательным в этом примере. Этот оператор извлекает имя приложения из свойства SourceApp. 

Теперь вы готовы использовать функцию applicationsScoping в межресурсном запросе.  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

Псевдоним функции возвращает объединение запросов из всех определенных приложений. После этого с помощью запроса фильтруются неудачные запросы и визуализируются тенденции по приложению.

![Пример результатов по нескольким запросам](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Запрос в ресурсах Application Insights и данных рабочей области 
При остановке работы Соединителя и необходимости выполнения запросов за промежуток времени, который был усечен при хранении данных Application Insights (90 дней), вам необходимо выполнить [межресурсные запросы](../../azure-monitor/log-query/cross-workspace-query.md) по рабочей области и ресурсам Application Insights за промежуточный период. Это происходит до тех пор, пока данные приложений не будут накапливаться в соответствии с новым параметром хранения данных Application Insights, упомянутым выше. Запрос требует некоторых манипуляций, так как схемы в Application Insights и в рабочей области отличаются. Различия схемы описаны в указанной ниже таблице в этом разделе. 

>[!NOTE]
>[Запрос между разными ресурсами](../log-query/cross-workspace-query.md) в оповещениях журнала поддерживается в новом API [правил запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). По умолчанию Azure Monitor использует [устаревшие API оповещения Log Analytics](../platform/api-alerts.md) для создания любого нового правила генерации оповещений на портале Azure, пока вы не переключаетесь с [устаревших API оповещений журнала](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). После переключения новый API используется по умолчанию для новых правил генерации оповещений на портале Azure и позволяет создавать правила генерации оповещений журнала запроса между разными ресурсами. Вы можете создать правила генерации оповещений журнала [запросов между разными ресурсами](../log-query/cross-workspace-query.md), не переключаясь, с помощью [шаблона ARM для API правил запросов по расписанию](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template), но этим правилом оповещения можно управлять с помощью [API правил запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), а не с помощью портала Azure.

Например, если соединитель перестал работать 2018-11-01, тогда при запросе журналов по ресурсам Application Insights и данным приложений в рабочей области ваш запрос будет построен следующим образом:

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Различия схем рабочей области Application Insights и Log Analytics
В следующей таблице показаны различия схем Log Analytics и Application Insights.  

| Свойства рабочей области Log Analytics| Свойства ресурса Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| "Обзор" | client_browser |
| City | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Country | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | success | 
| ResponseCode | resultCode | 
| Role | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | _url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Дальнейшие действия

Используйте [поиск по журналам](../../azure-monitor/log-query/log-query-overview.md), чтобы просматривать подробные сведения о приложениях Application Insights.
