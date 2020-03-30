---
title: Потоковая передача журналов платформы Azure в концентратор событий
description: Узнайте, как передавать журналы ресурсов Azure в концентратор событий для отправки данных во внешние системы, такие как сторонние SIEM и другие решения для анализа журналов.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658920"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Поток журналов платформы Azure для концентраторов событий Azure
[Платформы журналов](platform-logs-overview.md) в Azure, включая журналы деятельности Azure и журналы ресурсов, предоставляют подробную информацию о диагностике и аудите для ресурсов Azure и платформы Azure, от которой они зависят.  В этой статье описаны журналы потоковой платформы в концентраторы событий для отправки данных во внешние системы, такие как сторонние SIEMs и другие решения для анализа журналов.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>Что можно сделать с журналами платформ, отправленными в концентратор событий
Поток платформы журналы в Azure для событий концентраторы, чтобы обеспечить следующую функциональность:

* **Поток журналов для систем четных и телеметрических систем третьей стороны** - Поток всех журналов платформы в единый концентратор событий для трубного журнала данных стороннего sieM или инструмента анализа журналов.
  
* **Построить пользовательские телеметрии и лесозаготовки платформы** - высоко масштабируемые публиковать подписаться характер событий концентраторов позволяет гибко глотать платформы журналы в пользовательских телетритритрии платформы. Подробнее о [проектировании и подчете платформы телеметрии глобального масштаба можно узнать на концентрах Azure Event.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

* **Просмотр работоспособности служб путем потоковой передачи данных** в Power BI — используйте концентраторы событий, stream Analytics и Power BI для преобразования диагностических данных в практически в режиме реального времени, полученные в данных служб Azure. [См. Stream Analytics и Power BI: Панель мониторинга аналитики в режиме реального времени для потоковой передачи данных](../../stream-analytics/stream-analytics-power-bi-dashboard.md) для получения подробной информации об этом решении.

    Приведенный ниже пример SQL-запроса для Stream Analytics позволяет перенести все данные из журнала в таблицу Power BI:
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>Предварительные требования
Необходимо [создать концентратор событий,](../../event-hubs/event-hubs-create.md) если он еще не у вас есть. Если у вас уже есть диагностическая настройка, используюая это пространство имен событий концентратор, то этот концентратор событий будет повторно использован.

Политика общего доступа для пространства имен определяет разрешения, которыми обладает механизм потоковой передачи. Потоковая передача концентрам событий требует разрешения на управление, отправку и прослушивание. Можно создать или изменить общие политики доступа на портале Azure под вкладкой Настройка для пространства имен событий.

Чтобы обновить настройку диагностики, чтобы включить потоковую передачу, необходимо иметь разрешение ListKey на этом правиле авторизации концентраторов событий. Пространство имен службы "Центры событий Azure" не должно находиться в той же подписке, что и подписка, генерирующая журналы, если пользователь, который настраивает этот параметр, имеет соответствующий доступ RBAC к обеим подпискам, и обе подписки находятся в том же клиенте AAD.

## <a name="create-a-diagnostic-setting"></a>Создание параметра диагностики
Отправьте журналы платформы в концентратор событий и другие направления, создав диагностическую настройку для ресурса Azure. Смотрите [Создать диагностическую настройку для сбора журналов и метрик в Azure](diagnostic-settings.md) для получения подробной информации.

## <a name="collect-data-from-compute-resources"></a>Сбор данных из вычислительных ресурсов
Диагностические настройки будут собирать журналы ресурсов для ресурсов Azure, как и любой другой ресурс, но не их гостевой операционной системы или рабочих нагрузок. Чтобы собрать эти данные, установите [агент Log Analytics.](log-analytics-agent.md) 


## <a name="consuming-log-data-from-event-hubs"></a>Потребление данных журнала из концентраторов событий
Платформные журналы из концентраторов событий потребляются в формате JSON с элементами в следующей таблице.

| Имя элемента | Описание |
| --- | --- |
| records |Массив всех событий журнала, включенных в этот набор полезных данных. |
| time |Время возникновения события. |
| категория |Категория журнала для этого события. |
| resourceId |Идентификатор ресурса, который создал это событие. |
| operationName |Имя операции. |
| level |Необязательный параметр. Уровень ведения журнала событий. |
| properties |Свойства события. Они будут различаться для каждой службы Azure, как описано в разделе [](). |


Ниже приводится пример выходных данных из концентраторов событий для журнала ресурсов:

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```



## <a name="next-steps"></a>Дальнейшие действия

* [Подробнее о журналах ресурсов.](platform-logs-overview.md)
* [Создайте диагностическую настройку для сбора журналов и метрик в Azure.](diagnostic-settings.md)
* [Поток журналов активных каталогов С azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Начало работы с концентраторами событий](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

