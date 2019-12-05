---
title: Потоковая передача журналов ресурсов Azure в концентратор событий
description: Узнайте, как выполнять потоковую передачу журналов ресурсов Azure в концентратор событий для отправки данных внешним системам, таким как сторонние решения Siem и другие решения log Analytics.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 680570c5102f656b2b2d2e05f9e08f51fe892f44
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304952"
---
# <a name="stream-azure-resource-logs-to-azure-event-hubs"></a>Потоковая передача журналов ресурсов Azure в концентраторы событий Azure
[Журналы ресурсов](resource-logs-overview.md) в Azure предоставляют широкие и часто встречающиеся данные о внутренней работе ресурса Azure. В этой статье описываются журналы потоковой передачи ресурсов в концентраторы событий для отправки данных внешним системам, таким как сторонние решения Siem и другие решения log Analytics.


## <a name="what-you-can-do-with-resource-logs-sent-to-an-event-hub"></a>Что можно делать с журналами ресурсов, отправленными в концентратор событий
Потоковая передача журналов ресурсов в Azure в концентраторы событий для обеспечения следующих функциональных возможностей:

* **Потоковая передача журналов в сторонние системы ведения журналов и телеметрии** — потоковая передача всех журналов ресурсов в один концентратор событий для передачи данных журнала в СТОРОННЕЕ средство SIEM или log Analytics.
* **Создание настраиваемой платформы для телеметрии и ведения журналов** . Благодаря высоко масштабируемой природе концентраторов событий "публикация — подписка" можно гибко принимать журналы ресурсов в настраиваемую платформу телетри. Дополнительные сведения см. в статье [Разработка и изменение размера платформы телеметрии глобальной шкалы в концентраторах событий Azure](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .

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

## <a name="prerequisites"></a>предварительным требованиям
Если у вас еще нет [концентратора событий](../../event-hubs/event-hubs-create.md) , необходимо создать его. Если вы ранее перестроили журналы ресурсов в это пространство имен концентраторов событий, то этот концентратор событий будет использоваться повторно.

Политика общего доступа для пространства имен определяет разрешения, которые использует механизм потоковой передачи. Потоковая передача в концентраторы событий требует разрешений на управление, отправку и прослушивание. Политики общего доступа можно создать или изменить в портал Azure на вкладке Настройка для пространства имен концентраторов событий.

Чтобы обновить параметр диагностики для включения потоковой передачи, необходимо иметь разрешение ListKey для этого правила авторизации концентраторов событий. Пространство имен службы "Центры событий Azure" не должно находиться в той же подписке, что и подписка, генерирующая журналы, если пользователь, который настраивает этот параметр, имеет соответствующий доступ RBAC к обеим подпискам, и обе подписки находятся в том же клиенте AAD.

## <a name="create-a-diagnostic-setting"></a>Создание параметра диагностики
Журналы ресурсов по умолчанию не собираются. Отправьте их в концентратор событий и другие назначения, создав параметр диагностики для ресурса Azure. Дополнительные сведения см. в статье [Создание параметров диагностики для сбора журналов и метрик в Azure](diagnostic-settings.md) .

## <a name="stream-data-from-compute-resources"></a>Потоковая передача данных из ресурсов вычислений
Процесс, описанный в этой статье, предназначен для нерасчетных ресурсов, как описано в разделе [Общие сведения о журналах ресурсов Azure](diagnostic-settings.md).
Потоковая передача журналов ресурсов из ресурсов вычислений Azure с помощью агента система диагностики Azure Windows. Дополнительные сведения см. [в статье потоковая передача данных система диагностики Azure в критическом пути с помощью концентраторов событий](diagnostics-extension-stream-event-hubs.md) .


## <a name="consuming-log-data-from-event-hubs"></a>Использование данных журнала из концентраторов событий
При использовании журналов ресурсов из концентраторов событий он будет форматом JSON с элементами, приведенными в следующей таблице.

| Имя элемента | ОПИСАНИЕ |
| --- | --- |
| records |Массив всех событий журнала, включенных в этот набор полезных данных. |
| time |Время возникновения события. |
| категория |Категория журнала для этого события. |
| resourceId |Идентификатор ресурса, который создал это событие. |
| operationName |Имя операции. |
| уровень |необязательный параметр. Уровень ведения журнала событий. |
| properties |Свойства события. Они будут различаться для каждой службы Azure, как [ ]()описано в разделе. |


Ниже приведен пример выходных данных из концентраторов событий.

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



## <a name="next-steps"></a>Дополнительная информация

* [Потоковая передача журналов Azure Active Directory с Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Узнайте больше о журналах ресурсов Azure](resource-logs-overview.md).
* [Начало работы с концентраторами событий](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

