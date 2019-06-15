---
title: Действия веб-перехватчика для правил оповещений журнала в службе оповещений Azure
description: В этой статье описывается, как для правила генерации оповещений журнала с помощью log analytics рабочей области или application insights, передает данные в виде веб-перехватчика HTTP и сведения о вариантах настройки возможных.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 809c98c1e2e51ae51d7fe03f2165a5d9eecb05cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64681814"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Действия веб-перехватчика для правил оповещений журнала
При [создании оповещения в Azure](alerts-log.md) можно [настроить конфигурацию с помощью групп действий](action-groups.md) для выполнения одного или нескольких действий.  В этой статье описываются различные доступные действия веб-перехватчика и сведения о том, как настроить пользовательский веб-перехватчик на основе JSON.

> [!NOTE]
> Можно также использовать [общей схеме оповещений](https://aka.ms/commonAlertSchemaDocs), который предоставляет преимущества размещения одной расширяемой и единой полезных данных оповещения всех оповещений службы в Azure Monitor для интеграции веб-перехватчика. [Дополнительные сведения о стандартные определения оповещений схемы.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Действия webhook

Действия webhook позволяют вызывать внешний процесс с помощью одного запроса HTTP POST.  Вызываемая служба должна поддерживать веб-перехватчики и определить использование получаемых полезных данных.    

Для выполнения действий веб-перехватчика требуются свойства, приведенные в следующей таблице:

| Свойство | Описание |
|:--- |:--- |
| URL-адрес Webhook |URL-адрес действия webhook. |
| Настраиваемые полезные данные JSON |Настраиваемые полезные данные для отправки с помощью веб-перехватчика, если этот параметр выбран при создании оповещения. См. дополнительные сведения см. об [управлении оповещениями журналов](alerts-log.md). |

> [!NOTE]
> Кнопка просмотра веб-перехватчика (наряду с возможностью *включить настраиваемые полезные данные JSON для веб-перехватчика* в журнале оповещений) отобразит пример полезных данных веб-перехватчика для выполняемой настройки. Он не содержит фактические данные и представитель схемы JSON, используемой для журнала оповещений. 

Действия webhook включают URL-адрес и полезные данные в формате JSON, которые являются данными, отправляемыми во внешнюю службу.  По умолчанию полезные данные включают в себя значения из приведенной ниже таблицы.  Такие полезные данные можно заменить на собственные.  В этом случае можно использовать переменные из таблицы для каждого параметра, чтобы включить их значение в пользовательские полезные данные.


| Параметр | Переменная | Описание |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Имя правила генерации оповещений. |
| Severity |#severity |Уровень серьезности, установленный для срабатывающего оповещения журнала. |
| AlertThresholdOperator |#thresholdoperator |Оператор порога для правила генерации оповещений.  *Больше* или *Меньше*. |
| AlertThresholdValue |#thresholdvalue |Значение порога для правила генерации оповещений. |
| LinkToSearchResults |#linktosearchresults |Ссылка на портал Analytics, возвращающая записи из запроса, который создал оповещение. |
| ResultCount |#searchresultcount |Число записей в результатах поиска. |
| Время окончания интервала поиска |#searchintervalendtimeutc |Время завершения запроса в формате UTC (формат: мм/дд/гггг ЧЧ:мм:сс AM/PM) |
| Интервал поиска |#searchinterval |Временное окно для правила генерации оповещений (формат: ЧЧ:мм:сс) |
| Время начала интервала поиска |#searchintervalstarttimeutc |Время начала запроса в формате UTC (формат: мм/дд/гггг ЧЧ:мм:сс AM/PM) 
| SearchQuery |#searchquery |Запрос поиска журналов, используемый правилом генерации оповещений. |
| SearchResults |"IncludeSearchResults": true|Записи, возвращаемые запросом в виде JSON-таблицы, ограничены первой 1000 записей, если в определение настраиваемого веб-перехватчика JSON добавлен параметр "IncludeSearchResults":true в качестве свойства верхнего уровня. |
| WorkspaceID |#workspaceid |Идентификатор рабочей области Log Analytics. |
| Идентификатор приложения |#applicationid |Идентификатор вашего приложения Application Insight. |
| Идентификатор подписки |#subscriptionid |Идентификатор подписки Azure, используемой с Application Insights. 

> [!NOTE]
> LinkToSearchResults передает из URL-адреса такие параметры, как SearchQuery, время начала и завершения интервала поиска, на портал Azure для просмотра данных в разделе аналитики. Портал Azure имеет URI ограничение приблизительно 2000 знаков размера и будет *не* открыть ссылку в создает предупреждение, если значения параметров к превышению указанного. Пользователи могут ввести сведения вручную, чтобы просмотреть результаты на портале Analytics, либо получить результаты с помощью программных средств, используя [REST API для Application Insights Analytics](https://dev.applicationinsights.io/documentation/Using-the-API) или [для Log Analytics](/rest/api/loganalytics/). 

Например, можно указать следующие пользовательские полезные данные, содержащие один параметр — *text*.  Служба, вызываемая этим действием webhook, будет ожидать этот параметр.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
При отправке в webhook этот пример полезных данных разрешается в нечто следующее:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Так как все переменные в настраиваемом веб-перехватчике должны быть указаны в коде JSON в кавычках (например, "#searchinterval"), итоговый веб-перехватчик также будет содержать данные переменных в кавычках (например, "00:05:00").

Чтобы включить результаты поиска в настраиваемые полезные данные, добавьте **IncudeSearchResults** в полезные данные JSON как свойство верхнего уровня. 

## <a name="sample-payloads"></a>Примеры полезных данных
В этом разделе показан пример полезных данных для веб-перехватчика оповещений журнала, включая случаи стандартных и настраиваемых полезных данных.

### <a name="standard-webhook-for-log-alerts"></a>Стандартный веб-перехватчик для оповещений журнала 
В обоих этих примерах заданы фиктивные полезные данные, которые содержат только два столбца и две строки.

#### <a name="log-alert-for-azure-log-analytics"></a>Оповещение журнала для Azure Log-Analytics
Ниже приведен пример полезных данных для стандартного действия веб-перехватчика *без настраиваемого параметра JSON* при использовании в оповещениях на основе Log Analytics.

```json
{
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
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
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Warning"
 }
 ```

> [!NOTE]
> Значение поля серьезность может измениться, если у вас есть [переключить предпочитаемого API](alerts-log-api-switch.md) для оповещений журнала для Log Analytics.


#### <a name="log-alert-for-azure-application-insights"></a>Оповещение журнала для Azure Application Insights
Ниже приведен пример полезных данных для стандартного веб-перехватчика *без настраиваемого параметра JSON* при использовании в оповещениях журнала на основе Application Insights.
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
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
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://analytics.applicationinsights.io/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Оповещение журнала с настраиваемыми полезными данными JSON
Например, чтобы создать пользовательские полезные данные, включающие только имя оповещения и результаты поиска, выполните этот код: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Ниже приведен пример полезных данных для настраиваемого действия веб-перехватчика для любого оповещения журнала.
    
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


## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения см. в статье [Оповещения журнала в Azure Monitor. Интерфейс оповещений](alerts-unified-log.md).
- [Создание и просмотр оповещений журнала, а также управление ими с помощью Azure Monitor](alerts-log.md)
- Создание [групп действий в Azure](action-groups.md) и управление ими
- Дополнительные сведения об [Application Insights](../../azure-monitor/app/analytics.md)
- Дополнительные сведения о [журнал запросов](../log-query/log-query-overview.md). 

