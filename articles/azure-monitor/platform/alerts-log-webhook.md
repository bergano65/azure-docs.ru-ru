---
title: Действия Webhook для оповещений о журналах в оповещениях Azure
description: В этой статье описывается, как создать правило оповещения журнала с помощью рабочего пространства Log Analytics или Application Insights, как оповещение толкает данные как http webhook, и детали различных возможных настроек.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 7b1956ad2bf9bf38ba9edc4c7234078557564071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667709"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Действия веб-перехватчика для правил оповещений журнала
Когда [в Azure создается оповещение о журнале,](alerts-log.md)у вас есть возможность [настроить его с помощью групп действий](action-groups.md) для выполнения одного или нескольких действий. В этой статье описаны различные действия Webhook, которые доступны, и показано, как настроить пользовательский веб-крюк на основе JSON.

> [!NOTE]
> Вы также можете использовать [общую схему оповещения](https://aka.ms/commonAlertSchemaDocs) для интеграции веб-крючка. Общая схема оповещения обеспечивает преимущество наличия единой расширяемой и единой полезной нагрузки оповещения во всех службах оповещения в Azure Monitor.Please обратите внимание, что общая схема оповещения не соответствует пользовательскому варианту JSON для оповещений журнала. Он откладывает на общую полезную нагрузку схемы оповещения, если она выбрана независимо от настройки, которую вы могли бы сделать на уровне правила оповещения. [Узнайте об общих определениях схемы оповещения.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Действия webhook

С помощью действий Webhook можно вызвать внешний процесс через один запрос HTTP POST. Служба, которая называется, должна поддерживать веб-крючки и определять, как использовать любую полезную нагрузку, которую она получает.

Для выполнения действий webhook требуются свойства, приведенные в таблице ниже.

| Свойство | Описание |
|:--- |:--- |
| **URL-адрес Webhook** |URL-адрес действия webhook. |
| **Настраиваемые полезные данные JSON** |Пользовательская полезная нагрузка для отправки с веб-крючком, когда эта опция выбрана во время создания оповещения. Для получения дополнительной [информации см.](alerts-log.md)|

> [!NOTE]
> Кнопка **View Webhook** наряду с **пользовательской jSON полезной нагрузкой для webhook** для оповещения журнала отображает образец полезной нагрузки Webhook для предоставленной настройки. Он не содержит фактических данных, но является репрезентативным для схемы JSON, которая используется для оповещения о журналах. 

Webhooks включают URL и полезную нагрузку, отформатированные в JSON, которые отправляются во внешнюю службу. По умолчанию полезные данные включают в себя значения из приведенной ниже таблицы. Такие полезные данные можно заменить на собственные. В этом случае используйте переменные в таблице для каждого из параметров, чтобы включить их значения в пользовательскую полезную нагрузку.


| Параметр | Переменная | Описание |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Имя правила генерации оповещений. |
| *Серьезность* |#severity |Уровень серьезности, установленный для срабатывающего оповещения журнала. |
| *AlertThresholdOperator* |#thresholdoperator |Пороговый оператор для правила оповещения, которое использует больше или меньше, чем. |
| *AlertThresholdValue* |#thresholdvalue |Значение порога для правила генерации оповещений. |
| *LinkToSearchResults* |#linktosearchresults |Ссылка на портал Analytics, который возвращает записи из запроса, который создал оповещение. |
| *ResultCount* |#searchresultcount |Число записей в результатах поиска. |
| *Время окончания интервала поиска* |#searchintervalendtimeutc |Время окончания запроса в UTC, с форматом mm/dd/yyyy HH:mm:ss AM/PM. |
| *Интервал поиска* |#searchinterval |Временное окно для правила оповещения, с форматом HH:mm:ss. |
| *Время начала интервала поиска* |#searchintervalstarttimeutc |Время начала запроса в UTC, с форматом mm/dd/yyyy HH:mm:ss AM/PM. 
| *SearchQuery* |#searchquery |Запрос поиска журналов, используемый правилом генерации оповещений. |
| *SearchResults* |"IncludeSearchResults": true|Записи, возвращенные запросом в виде таблицы JSON, ограниченной первыми 1000 записями, если "IncludeSearchResults": правда добавлена в пользовательском определении JSON webhook как свойство верхнего уровня. |
| *Тип оповещения*| #alerttype | Правило предупреждения о типе журнала настроено как [метрическое измерение](alerts-unified-log.md#metric-measurement-alert-rules) или [количество результатов.](alerts-unified-log.md#number-of-results-alert-rules)|
| *WorkspaceID* |#workspaceid |Идентификатор рабочей области Log Analytics. |
| *Идентификатор приложения* |#applicationid |Идентификатор приложения Application Insights. |
| *Идентификатор подписки* |#subscriptionid |Id используемой подписки Azure. 

> [!NOTE]
> *LinkToSearchResults* передает параметры, такие как *поисковая кикерия,* *Время запуска интервала поиска*и время окончания *поиска* в URL-адресе на портал Azure для просмотра в разделе Analytics. Портал Azure имеет ограничение размера URI около 2000 символов. Портал *не* будет открывать ссылки, предоставляемые в оповещениях, если значения параметра превышают предел. Для просмотра результатов можно вручную ввести данные на портале Analytics. Или можно использовать [API Application Insights Analytics REST](https://dev.applicationinsights.io/documentation/Using-the-API) или [API Log Analytics REST](/rest/api/loganalytics/) для получения результатов в программном плане. 

Например, можно указать следующие пользовательские полезные данные, содержащие один параметр — *text*. Служба, которую вызывает этот веб-крюк, ожидает этого параметра.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Этот пример полезной нагрузки решает сяо, что-то вроде следующего, когда он отправляется в веб-крюк:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Поскольку все переменные в пользовательском веб-крючке должны быть указаны в корпусе JSON, как "#searchinterval", полученный webhook также имеет переменные данные внутри корпусов, как "00:05:00".

Чтобы включить результаты поиска в пользовательскую полезную нагрузку, убедитесь, что **IncludeSearchResults** установлен в качестве свойства верхнего уровня в полезной нагрузке JSON. 

## <a name="sample-payloads"></a>Примеры полезных данных
В этом разделе показаны примеры полезной нагрузки для веб-крючков для оповещений о журналах. В примере полезной нагрузки входят примеры, когда полезная нагрузка является стандартной и когда она пользовательская.

### <a name="standard-webhook-for-log-alerts"></a>Стандартный веб-крюк для оповещений о журналах 
Оба этих примера имеют фиктивную полезную нагрузку только с двумя колоннами и двумя рядами.

#### <a name="log-alert-for-log-analytics"></a>Предупреждение о журнале для аналитики журналов
Следующий образец полезной нагрузки предназначен для стандартного действия webhook *без пользовательского варианта JSON,* который используется для оповещений на основе log Analytics:

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
> Значение поля "Severity" может измениться, если вы [переключили настройки API](alerts-log-api-switch.md) на оповещения о журнале в журнале Analytics.


#### <a name="log-alert-for-application-insights"></a>Предупреждение о входе для исследования приложений
Следующий образец полезной нагрузки предназначен для стандартного веб-крючка *без пользовательского варианта JSON,* когда он используется для оповещений о журнале на основе application Insights:
    
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

#### <a name="log-alert-with-custom-json-payload"></a>Оповещение о журнале с пользовательской полезной нагрузкой JSON
Например, для создания пользовательской полезной нагрузки, которая включает только имя оповещения и результаты поиска, можно использовать следующее: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Следующий образец полезной нагрузки для пользовательского действия webhook для любого журнала оповещения:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
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


## <a name="next-steps"></a>Дальнейшие действия
- Узнайте о [оповещениях о журналах в оповещениях Azure.](alerts-unified-log.md)
- Понять, как [управлять оповещениями о журналах в Azure.](alerts-log.md)
- Создавайте и управляйте [группами действий в Azure.](action-groups.md)
- Дополнительные сведения об [Application Insights](../../azure-monitor/app/analytics.md).
- Подробнее о [запросах журнала](../log-query/log-query-overview.md). 

