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
ms.openlocfilehash: d441b72b34da6146eba523563a09c2908cdcbbf4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650132"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Объединение нескольких ресурсов Azure Monitor Application Insights 
В этой статье описывается, как запрашивать и просматривать все данные журнала Application Insights в одном месте, даже если они находятся в разных подписках Azure, в качестве замены нерекомендуемых Соединитель Application Insights. Количество Application Insights ресурсов, которые можно включить в один запрос, ограничено 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Рекомендуемый подход для запроса нескольких ресурсов Application Insights 
Перечисление нескольких ресурсов Application Insights в запросе может быть весьма трудоемким и трудным в поддержке процессом. Вместо этого вы можете использовать функции для отделения логики запросов и области приложений.  

В примере ниже показано, как отслеживать несколько ресурсов Application Insights и визуализировать количество неудачных запросов по имени приложения. Перед началом работы выполните этот запрос в рабочей области, которая подключена к ресурсам Application Insights, чтобы получить список подключенных приложений: 

```
ApplicationInsights
| summarize by ApplicationName
```

Создайте функцию с помощью оператора union со списком приложений, затем сохраните запрос в рабочей области в качестве функции с псевдонимом *applicationsScoping*. 

Вы можете в любое время изменить список приложений на портале, перейдя к обозревателю запросов в рабочей области и выбрав функцию для редактирования с последующим сохранением, или с помощью командлета PowerShell `SavedSearch`. 

>[!NOTE]
>Этот метод нельзя использовать с оповещениями журнала, так как проверка доступа к ресурсам правила генерации оповещений, включая рабочие области и приложения, выполняется во время создания предупреждения. Добавление новых ресурсов в функцию после создания предупреждения не поддерживается. Если вы предпочитаете использовать функцию для определения области ресурсов в оповещениях журнала, необходимо изменить правило генерации оповещений на портале или с помощью шаблона диспетчер ресурсов, чтобы обновить ресурсы с областью действия. Кроме того, можно включить список ресурсов в запрос оповещения журнала.

Команда `withsource= SourceApp` позволяет добавить в результаты столбец, который обозначает приложение, отправившее журнал. Оператор Parse необязателен в этом примере и использует для извлечения имени приложения из свойства Саурцеапп. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Теперь вы готовы использовать функцию applicationsScoping в межресурсном запросе.  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

Запрос использует схему Application Insights, несмотря на то что запрос выполняется в рабочей области, так как функция applicationsScoping возвращает структуру данных Application Insights. Псевдоним функции возвращает объединение запросов из всех определенных приложений. После этого с помощью запроса фильтруются неудачные запросы и визуализируются тенденции по приложению.

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
| AvailabilityRunLocation | расположение |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| Browser | client_browser |
| Город | client_city |
| ClientIP | client_IP |
| Компьютер | cloud_RoleInstance | 
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
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Следующие шаги

Используйте [поиск по журналам](../../azure-monitor/log-query/log-query-overview.md), чтобы просматривать подробные сведения о приложениях Application Insights.
