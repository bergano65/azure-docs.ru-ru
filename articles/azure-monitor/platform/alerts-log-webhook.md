---
title: Действия веб-перехватчика для оповещений журнала в службе оповещений Azure
description: В этой статье описывается, как создать правило генерации оповещений журнала с помощью рабочей области Log Analytics или Application Insights как оповещение передает данные в виде веб-перехватчика HTTP и сведения о возможных вариантах настройки.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 6aa007c621e76cb0c188a7dab6279fd9e387b2b3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705190"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Действия веб-перехватчика для правил оповещений журнала
Когда [оповещения журнала создается в Azure](alerts-log.md), у вас есть возможность [настройки его с помощью групп действий](action-groups.md) для выполнения одного или нескольких действий. В этой статье описываются доступные действия другой веб-перехватчика и показано, как настроить пользовательские на основе JSON веб-перехватчика.

> [!NOTE]
> Вы также можете использовать [общей схеме оповещений](https://aka.ms/commonAlertSchemaDocs) для интеграции веб-перехватчика. Общая схема предупреждения предоставляет преимущества размещения одной расширяемой и единой полезных данных оповещения по всем службам, оповещения в Azure Monitor. [Дополнительные сведения о стандартные определения оповещений схемы.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Действия webhook

С действиями веб-перехватчика можно вызвать внешний процесс посредством одного запроса HTTP POST. Служба, которая вызывается должна поддерживать веб-перехватчики и определить способы использования все полезные данные, которые он получает.

Для выполнения действий webhook требуются свойства, приведенные в таблице ниже.

| Свойство | Описание |
|:--- |:--- |
| **URL-адрес Webhook** |URL-адрес действия webhook. |
| **Настраиваемые полезные данные JSON** |Настраиваемые полезные данные для отправки с webhook при выборе этого параметра во время создания предупреждения. Дополнительные сведения см. в разделе [Управление оповещениями журнала](alerts-log.md).|

> [!NOTE]
> **Представление веб-перехватчика** кнопку рядом с **включить настраиваемые полезные данные JSON для веб-перехватчика** параметр для оповещения журнала отображается пример полезных данных веб-перехватчика для настройки, который был предоставлен. Он не содержит фактические данные, но является представитель схемы JSON, который используется для оповещений журнала. 

Действия Webhook включают URL-адрес и полезных данных в формате JSON, что данные отправлены с внешними службами. По умолчанию полезные данные включают в себя значения из приведенной ниже таблицы. Такие полезные данные можно заменить на собственные. Используйте переменные в этом случае в таблице для каждого параметра для включения их значений в настраиваемые полезные данные.


| Параметр | Переменная | Описание |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Имя правила генерации оповещений. |
| *Уровень серьезности* |#severity |Уровень серьезности, установленный для срабатывающего оповещения журнала. |
| *AlertThresholdOperator* |#thresholdoperator |Оператор порога для правила генерации оповещений, который использует больше или меньше чем. |
| *AlertThresholdValue* |#thresholdvalue |Значение порога для правила генерации оповещений. |
| *LinkToSearchResults* |#linktosearchresults |Ссылка на портале аналитики, возвращающая записи из запроса, который его создал. |
| *ResultCount* |#searchresultcount |Число записей в результатах поиска. |
| *Время окончания интервала поиска* |#searchintervalendtimeutc |Время окончания для запроса в формате UTC в формате мм/дд/гггг чч: мм: сс AM/PM. |
| *Интервал поиска* |#searchinterval |Временное окно для правила генерации оповещений, в формате чч: мм:. |
| *Время начала интервала поиска* |#searchintervalstarttimeutc |Время начала для запроса в формате UTC в формате мм/дд/гггг чч: мм: сс AM/PM. 
| *SearchQuery* |#searchquery |Запрос поиска журналов, используемый правилом генерации оповещений. |
| *SearchResults* |"IncludeSearchResults": true|Записей, возвращенных при выполнении запроса как JSON-таблицы, ограничены первой 1000 записей, если параметр «IncludeSearchResults»: true добавляется в определение настраиваемого веб-перехватчика JSON как свойство верхнего уровня. |
| *Тип оповещения*| #alerttype | Тип правила генерации оповещений журнала настроен в качестве [измерение метрик](alerts-unified-log.md#metric-measurement-alert-rules) или [количество результатов](alerts-unified-log.md#number-of-results-alert-rules).|
| *ИД рабочей области* |#workspaceid |Идентификатор рабочей области Log Analytics. |
| *Идентификатор приложения* |#applicationid |Application Insights идентификатор приложения. |
| *Идентификатор подписки* |#subscriptionid |Идентификатор подписки Azure, которая используется. 

> [!NOTE]
> *LinkToSearchResults* передает параметры, например *SearchQuery*, *время начала интервала поиска*, и *время окончания интервала поиска* в URL-адрес в Azure портал для просмотра в разделе «аналитика». Портал Azure имеет ограничение размера URI около 2 000 символов. Портал будет *не* откройте ссылки, приведенные в предупреждения, если значение параметра превышает предел. Можно вручную ввести сведения, чтобы просмотреть результаты на портале аналитики. Можно также использовать [Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API) или [Log Analytics REST API](/rest/api/loganalytics/) для получения результатов программным способом. 

Например, можно указать следующие пользовательские полезные данные, содержащие один параметр — *text*. Служба, которая вызывает этот веб-перехватчик ожидает, что этот параметр.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Этот пример полезных данных разрешается в приведенную ниже, при отправке веб-перехватчика:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Так как все переменные в настраиваемые веб-перехватчика должен быть указан в корпусе JSON, например «#searchinterval», результирующий веб-перехватчика также имеет динамических данных внутри корпуса, как «00: 05:00.»

Чтобы включить результаты поиска в пользовательские полезные данные, убедитесь, что **параметр IncludeSearchResults** задается как свойство верхнего уровня в полезных данных JSON. 

## <a name="sample-payloads"></a>Примеры полезных данных
В этом разделе показаны примеры полезных данных для веб-перехватчиков для оповещений журнала. Примеры полезных данных содержат примеры, при "стандартный" и когда это пользовательские полезные данные.

### <a name="standard-webhook-for-log-alerts"></a>Стандартный веб-перехватчика для оповещений журнала 
Обе эти примеры имеют фиктивные полезные данные только два столбца и две строки.

#### <a name="log-alert-for-log-analytics"></a>Оповещение журнала для Log Analytics
Следующий пример полезных данных — для стандартного веб-перехватчика действия *без настраиваемого параметра JSON* , используемый для оповещений, основанное на Log Analytics:

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> Значение поля «Severity» может измениться, если вы уже [переключить предпочитаемого API](alerts-log-api-switch.md) для оповещений журнала для Log Analytics.


#### <a name="log-alert-for-application-insights"></a>Оповещение журнала для Application Insights
Следующий пример полезных данных — для стандартного веб-перехватчика *без настраиваемого параметра JSON* при использовании для оповещений журнала на основе Application Insights:
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Оповещение журнала с настраиваемыми полезными данными JSON
Например чтобы создать пользовательские полезные данные, включающие только имя оповещения и результаты поиска, можно использовать следующее: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Следующий пример полезных данных — для действия пользовательского веб-перехватчика для любого оповещения журнала:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>Следующие шаги
- Дополнительные сведения о [оповещения журнала в службе оповещений Azure](alerts-unified-log.md).
- Понять, как [Управление оповещениями журнала в Azure](alerts-log.md).
- Создание и управление ими [групп действий в Azure](action-groups.md).
- Дополнительные сведения об [Application Insights](../../azure-monitor/app/analytics.md).
- Дополнительные сведения о [журнал запросов](../log-query/log-query-overview.md). 

