---
title: Потоковая передача журналов платформы Azure в концентратор событий
description: Узнайте, как выполнять потоковую передачу журналов ресурсов Azure в концентратор событий для отправки данных внешним системам, таким как сторонние решения Siem и другие решения log Analytics.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 00dcc1c1a1d823ab0f2497e47641916d391ee37b
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750355"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Потоковая передача журналов платформы Azure в концентраторы событий Azure
[Журналы платформы](platform-logs-overview.md) в Azure, в том числе журнал действий Azure и журналы ресурсов, предоставляют подробные сведения о диагностике и аудите для ресурсов Azure и платформы Azure, от которых они зависят.  В этой статье описываются журналы платформы потоковой передачи в концентраторы событий для отправки данных внешним системам, таким как сторонние решения Siem и другие решения log Analytics.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>Что можно делать с журналами платформы, отправленными в концентратор событий
Потоковая передача журналов платформы в Azure в концентраторы событий для обеспечения следующих функциональных возможностей:

* **Потоковая передача журналов в сторонние системы ведения журналов и телеметрии** — потоковая передача всех журналов вашей платформы в один концентратор событий для передачи данных журнала в СТОРОННИЕ средства SIEM или log Analytics.
  
* **Создание настраиваемой платформы для телеметрии и ведения журналов** . Благодаря высоко масштабируемой природе концентраторов событий "публикация — подписка" можно гибко принимать журналы платформы в настраиваемую платформу телетри. Дополнительные сведения см. в статье [Разработка и изменение размера платформы телеметрии глобальной шкалы в концентраторах событий Azure](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .

* **Просматривайте данные о работоспособности службы путем потоковой передачи данных в Power BI** — используйте концентраторы событий, Stream Analytics и Power BI для преобразования диагностических данных в почти в реальном времени аналитические сведения о службах Azure. Дополнительные сведения об этом решении см. в разделе [Stream Analytics и Power BI. панель мониторинга для потоковой передачи данных в режиме реального времени](../../stream-analytics/stream-analytics-power-bi-dashboard.md) .

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

## <a name="prerequisites"></a>Технические условия
Если у вас еще нет [концентратора событий](../../event-hubs/event-hubs-create.md) , необходимо создать его. Если у вас уже есть параметр диагностики, использующий это пространство имен концентраторов событий, то этот концентратор событий будет использоваться повторно.

Политика общего доступа для пространства имен определяет разрешения, которые использует механизм потоковой передачи. Потоковая передача в концентраторы событий требует разрешений на управление, отправку и прослушивание. Политики общего доступа можно создать или изменить в портал Azure на вкладке Настройка для пространства имен концентраторов событий.

Чтобы обновить параметр диагностики для включения потоковой передачи, необходимо иметь разрешение ListKey для этого правила авторизации концентраторов событий. Пространство имен службы "Центры событий Azure" не должно находиться в той же подписке, что и подписка, генерирующая журналы, если пользователь, который настраивает этот параметр, имеет соответствующий доступ RBAC к обеим подпискам, и обе подписки находятся в том же клиенте AAD.

## <a name="create-a-diagnostic-setting"></a>Создание параметра диагностики
Отправьте журналы платформы в концентратор событий и другие назначения, создав параметр диагностики для ресурса Azure. Дополнительные сведения см. в статье [Создание параметров диагностики для сбора журналов и метрик в Azure](diagnostic-settings.md) .

## <a name="collect-data-from-compute-resources"></a>Получение данных из ресурсов вычислений
Параметры диагностики будут выполнять сбор журналов ресурсов для ресурсов Azure, таких как любые другие ресурсы, но не их гостевой операционной системы или рабочих нагрузок. Чтобы получить эти данные, установите [агент log Analytics](log-analytics-agent.md). 


## <a name="consuming-log-data-from-event-hubs"></a>Использование данных журнала из концентраторов событий
Журналы платформы из концентраторов событий используются в формате JSON с элементами, приведенными в следующей таблице.

| Имя элемента | Description |
| --- | --- |
| records |Массив всех событий журнала, включенных в этот набор полезных данных. |
| time |Время возникновения события. |
| категория |Категория журнала для этого события. |
| resourceId |Идентификатор ресурса, который создал это событие. |
| operationName |Имя операции. |
| level |Необязательный параметр. Уровень ведения журнала событий. |
| properties |Свойства события. Они будут различаться для каждой службы Azure, как описано в разделе [](). |


Ниже приведен пример выходных данных из концентраторов событий для журнала ресурсов.

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

* [Дополнительные сведения см. в статье о журналах ресурсов](platform-logs-overview.md).
* [Создайте параметр диагностики для сбора журналов и метрик в Azure](diagnostic-settings.md).
* [Потоковая передача журналов Azure Active Directory с Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Начало работы с концентраторами событий](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

