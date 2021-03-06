---
title: Мониторинг фабрик данных с помощью Azure Monitor
description: Узнайте, как использовать Azure Monitor для мониторинга конвейеров фабрики данных/Азуре, включив журналы диагностики со сведениями из фабрики данных.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: aaa67c5e48c6246e94410bdbf2eb8509b6810001
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645197"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Предупреждения и мониторинг фабрик данных с помощью Azure Monitor

Облачные приложения являются сложными и имеют много движущихся частей. Мониторы предоставляют данные, помогающие обеспечить работоспособность приложений. Мониторы также помогают избежать потенциальных проблем и устранять неполадки в прошлом.

Данные мониторинга можно использовать для получения глубокой информации о приложениях. Эти знания помогут повысить производительность и удобство обслуживания приложений. Она также помогает автоматизировать действия, которые в противном случае требуется вмешательство вручную.

Azure Monitor предоставляет метрики и журналы инфраструктуры базового уровня для большинства служб Azure. Журналы диагностики Azure выдаются ресурсом и предоставляют обширные, часто встречающиеся данные о работе этого ресурса. И фабрика данных Azure записывает журналы диагностики в монитор.

Дополнительные сведения см. в разделе [Общие сведения о Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-data"></a>Хранение данных фабрики данных Azure

В фабрике данных хранится конвейер — данные запускаются только в течение 45 дней. Используйте монитор, если хотите, чтобы данные оставались в течение более длительного времени. С помощью монитора можно направить журналы диагностики для анализа. Вы также можете хранить их в учетной записи хранения, чтобы получить сведения о фабрике на выбранный период времени.

## <a name="diagnostic-logs"></a>Журналы диагностики

* Сохраните журналы диагностики в учетной записи хранения для аудита или проверки вручную. Параметры диагностики можно использовать для указания времени хранения в днях.
* Потоковая передача журналов в концентраторы событий Azure. Журналы становятся входными данными для партнерской службы или пользовательского решения аналитики, например Power BI.
* Проанализируйте журналы с помощью Log Analytics.

Вы можете использовать учетную запись хранения или пространство имен концентратора событий, не подписку на ресурс, который создает журналы. Пользователь, который настраивает параметр, должен иметь соответствующий доступ к обеим подпискам с помощью управления доступом на основе ролей (RBAC).

## <a name="set-up-diagnostic-logs"></a>Настройка журналов диагностики

### <a name="diagnostic-settings"></a>Параметры диагностики

Используйте параметры диагностики, чтобы настроить журналы диагностики для невычисленийных ресурсов. Параметры для элемента управления ресурсами имеют следующие характеристики.

* Они указывают, куда отправляются журналы диагностики. Примеры включают учетную запись хранения Azure, концентратор событий Azure или журналы мониторинга.
* Они указывают, какие категории журналов отправляются.
* Они определяют, как долго каждая категория журнала должна храниться в учетной записи хранения.
* Срок хранения 0 дней означает, что журналы хранятся неограниченно долго. В противном случае значение может быть любым числом дней от 1 до 2 147 483 647.
* Если политики хранения заданы, но хранение журналов в учетной записи хранения отключено, политики хранения не будут действовать. Например, это может произойти, если выбраны только параметры концентраторов событий или мониторинга журналов.
* Политики хранения применяются в день. Граница между днями выполняется в полночь в формате UTC. В конце дня журналы удаляются из числа дней, которые выходят за пределы политики хранения. Например, если у вас есть политика хранения на один день, то в начале текущей версии журналы с до вчерашнего дня удаляются.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Включение журналов диагностики с помощью Azure Monitor REST API

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Создание или обновление параметра диагностики в мониторе REST API

##### <a name="request"></a>Запрос

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Заголовки

* Замените `{api-version}` на `2016-09-01`.
* Замените `{resource-id}` ИДЕНТИФИКАТОРом ресурса, параметры диагностики которого необходимо изменить. Дополнительные сведения см. в разделе [Использование групп ресурсов для управления ресурсами Azure](../azure-resource-manager/manage-resource-groups-portal.md).
* В качестве заголовка `Content-Type` установите `application/json`.
* Задайте заголовок авторизации для веб-токена JSON, полученного из Azure Active Directory (Azure AD). Дополнительные сведения см. в статье [Сценарии аутентификации в Azure Active Directory](../active-directory/develop/authentication-scenarios.md).

##### <a name="body"></a>Текст

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Свойство | Тип | Description |
| --- | --- | --- |
| **сторажеаккаунтид** |String | Идентификатор ресурса учетной записи хранения, в которую необходимо отправить журналы диагностики. |
| **Запросе** |String | Идентификатор правила служебной шины для пространства имен Service-Bus, в котором должны быть созданы концентраторы событий для потоковой передачи журналов диагностики. Идентификатор правила имеет `{service bus resource ID}/authorizationrules/{key name}`формата.|
| **workspaceId** | Сложный тип | Массив временных граней метрики и их политик хранения. Значение этого свойства пусто. |
|**metrics**| Значения параметров выполнения конвейера, которые должны быть переданы в вызываемый конвейер.| Объект JSON, который сопоставляет имена параметров со значениями аргументов. |
| **журналы**| Сложный тип| Имя категории журнала диагностики для типа ресурса. Чтобы получить список категорий журналов диагностики для ресурса, выполните операцию получения диагностической настройки. |
| **category**| String| Массив категорий журналов и их политик хранения. |
| **timegrain** | String | Гранулярность метрик, которые записываются в формате длительности ISO 8601. Значение свойства должно быть `PT1M`, которое указывает одну минуту. |
| **доступной**| Логическое | Указывает, включен ли сбор метрики или категории журналов для этого ресурса. |
| **retentionPolicy**| Сложный тип| Описывает политику хранения метрики или категории журналов. Это свойство используется только для учетных записей хранения. |
|**недели**| Int| Количество дней, в течение которых хранятся метрики или журналы. Если значение свойства равно 0, журналы хранятся неограниченное время. Это свойство используется только для учетных записей хранения. |

##### <a name="response"></a>Ответ

200 ОК.


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Получение сведений о параметрах диагностики на мониторе REST API

##### <a name="request"></a>Запрос

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Заголовки

* Замените `{api-version}` на `2016-09-01`.
* Замените `{resource-id}` ИДЕНТИФИКАТОРом ресурса, параметры диагностики которого необходимо изменить. Дополнительные сведения см. в разделе [Использование групп ресурсов для управления ресурсами Azure](../azure-resource-manager/manage-resource-groups-portal.md).
* В качестве заголовка `Content-Type` установите `application/json`.
* Задайте заголовок авторизации для веб-токена JSON, полученного из Azure AD. Дополнительные сведения см. в статье [Сценарии аутентификации в Azure Active Directory](../active-directory/develop/authentication-scenarios.md).

##### <a name="response"></a>Ответ

200 ОК.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}

```
Дополнительные сведения см. в разделе [параметры диагностики](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Схема журналов и событий

### <a name="monitor-schema"></a>Схема монитора

#### <a name="activity-run-log-attributes"></a>Действия — выполнение атрибутов журнала

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Свойство | Тип | Description | Пример |
| --- | --- | --- | --- |
| **Level** |String | Уровень журналов диагностики. Для журналов выполнения действий задайте для свойства значение 4. | `4` |
| **correlationId** |String | Уникальный идентификатор для отслеживания конкретного запроса. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Время события в формате TimeSpan UTC `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**активитирунид**| String| Идентификатор выполнения действия. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**пипелинерунид**| String| Идентификатор выполнения конвейера. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | Идентификатор, связанный с ресурсом фабрики данных. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | Категория журналов диагностики. В качестве значения свойства задайте `ActivityRuns`. | `ActivityRuns` |
|**level**| String | Уровень журналов диагностики. В качестве значения свойства задайте `Informational`. | `Informational` |
|**operationName**| String | Имя действия с его состоянием. Если действие является начальным пульсом, значение свойства равно `MyActivity -`. Если действие является конечным пульсом, значение свойства равно `MyActivity - Succeeded`. | `MyActivity - Succeeded` |
|**пипелиненаме**| String | Имя конвейера. | `MyPipeline` |
|**activityName**| String | Имя действия. | `MyActivity` |
|**start**| String | Время начала действия выполняется в формате времени в формате UTC. | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | Время окончания действия выполняется в формате времени в формате UTC. Если в журнале диагностики указано, что действие началось, но еще не завершено, значение свойства равно `1601-01-01T00:00:00Z`. | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Конвейер — выполнение атрибутов журнала

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Свойство | Тип | Description | Пример |
| --- | --- | --- | --- |
| **Level** |String | Уровень журналов диагностики. Для журналов выполнения действий задайте для свойства значение 4. | `4` |
| **correlationId** |String | Уникальный идентификатор для отслеживания конкретного запроса. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Время события в формате TimeSpan UTC `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| Идентификатор выполнения конвейера. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | Идентификатор, связанный с ресурсом фабрики данных. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | Категория журналов диагностики. В качестве значения свойства задайте `PipelineRuns`. | `PipelineRuns` |
|**level**| String | Уровень журналов диагностики. В качестве значения свойства задайте `Informational`. | `Informational` |
|**operationName**| String | Имя конвейера и его состояние. После завершения выполнения конвейера значение свойства будет `Pipeline - Succeeded`. | `MyPipeline - Succeeded`. |
|**пипелиненаме**| String | Имя конвейера. | `MyPipeline` |
|**start**| String | Время начала действия выполняется в формате времени в формате UTC. | `2017-06-26T20:55:29.5007959Z`. |
|**end**| String | Время окончания действия выполняется в формате времени в формате UTC. Если в журнале диагностики указано, что действие началось, но еще не завершено, значение свойства равно `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |
|**status**| String | Конечное состояние выполнения конвейера. Возможные значения свойств — `Succeeded` и `Failed`. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Атрибуты журнала запуска триггера

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| Свойство | Тип | Description | Пример |
| --- | --- | --- | --- |
| **Level** |String | Уровень журналов диагностики. Для журналов выполнения действий задайте для свойства значение 4. | `4` |
| **correlationId** |String | Уникальный идентификатор для отслеживания конкретного запроса. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Время события в формате TimeSpan UTC `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| Идентификатор запуска триггера. | `08587023010602533858661257311` |
|**resourceId**| String | Идентификатор, связанный с ресурсом фабрики данных. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | Категория журналов диагностики. В качестве значения свойства задайте `PipelineRuns`. | `PipelineRuns` |
|**level**| String | Уровень журналов диагностики. В качестве значения свойства задайте `Informational`. | `Informational` |
|**operationName**| String | Имя триггера с окончательным состоянием, которое указывает, успешно ли запущен триггер. Если пульс прошел успешно, значение свойства равно `MyTrigger - Succeeded`. | `MyTrigger - Succeeded` |
|**triggerName**| String | Имя триггера. | `MyTrigger` |
|**triggerType может принимать**| String | Тип триггера. Возможные значения свойств — `Manual Trigger` и `Schedule Trigger`. | `ScheduleTrigger` |
|**тригжеревент**| String | Событие триггера. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | Время начала срабатывания триггера в формате TimeSpan UTC. | `2017-06-26T20:55:29.5007959Z`|
|**status**| String | Конечное состояние, показывающее, успешно ли вызван триггер. Возможные значения свойств — `Succeeded` и `Failed`. | `Succeeded`|

### <a name="log-analytics-schema"></a>Схема Log Analytics

Log Analytics наследует схему от Monitor со следующими исключениями:

* Первая буква в имени каждого столбца задается прописной буквой. Например, имя столбца «correlationId» в параметре Monitor имеет значение «CorrelationId» в Log Analytics.
* Нет столбца "Level".
* Динамический столбец "Properties" сохраняется как следующий динамический тип больших двоичных объектов JSON.

    | Azure Monitor столбец | Log Analytics столбец | Тип |
    | --- | --- | --- |
    | $. Properties. усерпропертиес | UserProperties | Динамические |
    | $. Properties. Примечания | Заметки | Динамические |
    | $. Properties. Входной | Входные данные | Динамические |
    | $. Properties. Проверки | Выходные данные | Динамические |
    | $. Properties. Ошибка. errorCode | ErrorCode | int |
    | $. Properties. Ошибка. сообщение | ErrorMessage | string |
    | $. Properties. План | Ошибка | Динамические |
    | $. Properties. Предшественников | Предшественников | Динамические |
    | $. Properties. Вход | Параметры | Динамические |
    | $. Properties. SystemParameters | SystemParameters | Динамические |
    | $. Properties. Тэги | Теги | Динамические |
    
## <a name="metrics"></a>Метрики

С помощью монитора можно получить представление о производительности и работоспособности рабочих нагрузок Azure. Наиболее важным типом данных монитора является метрика, которая также называется счетчиком производительности. Метрики создаются большинством ресурсов Azure. Монитор предоставляет несколько способов настройки и использования этих метрик для мониторинга и устранения неполадок.

Фабрика данных Azure версии 2 выдает следующие метрики.

| **Метрика**           | **Отображаемое имя метрики**         | **Единица измерения** | **Тип агрегирования** | **Описание**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Метрики успешных выполнений конвейеров. | Количество    | Итого                | Общее количество выполнений конвейеров, выполненных в течение минуты. |
| PipelineFailedRuns   | Метрики неудачных выполнений конвейеров.    | Количество    | Итого                | Общее число запусков конвейера, завершившихся сбоем, в течение минуты.    |
| ActivitySucceededRuns | Метрики успешных выполнений действий. | Количество    | Итого                | Общее число выполнений действий, выполненных в течение минуты.  |
| ActivityFailedRuns   | Метрики неудачных выполнений действий.    | Количество    | Итого                | Общее число выполнений действий, завершившихся сбоем, в течение минуты.     |
| TriggerSucceededRuns | Метрики успешных запусков триггеров.  | Количество    | Итого                | Общее число запусков триггеров, выполненных в течение минуты.   |
| TriggerFailedRuns    | Метрики неудачных запусков триггеров.     | Количество    | Итого                | Общее число выполнений триггеров в течение минуты.      |

Чтобы получить доступ к метрикам, выполните инструкции в разделе [Azure Monitor Data Platform](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

> [!NOTE]
> Создаются только завершенные, активированные действия и запуски конвейера. Выполняющиеся и песочницы/Отладка **не** создаются. 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Мониторинг метрик фабрики данных с помощью Azure Monitor

Вы можете использовать интеграцию фабрики данных с монитором для маршрутизации данных для мониторинга. Это удобно в следующих случаях:

* Необходимо писать сложные запросы к широкому набору метрик, публикуемых фабрикой данных для отслеживания. С помощью монитора можно создавать пользовательские предупреждения для этих запросов.

* Требуется отслеживать фабрики данных. Вы можете направить данные из нескольких фабрик данных в одну рабочую область монитора.

Уделите 7 минут своего времени, чтобы просмотреть следующее видео с кратким обзором и демонстрацией этой функции.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Настройка параметров и рабочей области диагностики

Создайте или добавьте параметры диагностики для фабрики данных.

1. На портале перейдите к разделу монитор. Выберите **параметры** > **параметры диагностики**.

1. Выберите фабрику данных, для которой необходимо задать параметр диагностики.

1. Если в выбранной фабрике данных не существует параметров, вам будет предложено создать параметр. Выберите **Включить диагностику**.

   ![Создать параметр диагностики, если параметры не существуют](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Если в фабрике данных есть существующие параметры, отобразится список параметров, уже настроенных в фабрике данных. Выберите **Добавить параметр диагностики**.

   ![Добавить параметр диагностики, если параметры существуют](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Присвойте параметру имя, выберите **отправить log Analytics**, а затем выберите рабочую область из **log Analytics рабочей области**.

    ![Назовите свои параметры и выберите рабочую область log-Analytics.](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. Щелкните **Сохранить**.

Через несколько секунд в списке параметров для этой фабрики данных появится новый параметр. Журналы диагностики передаются в эту рабочую область, как только создаются новые данные о событиях. Между созданием события и его отображением в Log Analytics может пройти до 15 минут.

* В режиме _конкретного ресурса_ журналы диагностики из фабрики данных Azure поступают в таблицы _адфпипелинерун_, _адфтригжеррун_и _адфактивитирун_ .
* В режиме _Azure-Diagnostics_ журналы диагностики поступают в таблицу _AzureDiagnostics_.

> [!NOTE]
> Так как таблица журналов Azure не может содержать более 500 столбцов, мы настоятельно рекомендуем выбрать режим для конкретного ресурса. Дополнительные сведения см. в разделе [log Analytics известные ограничения](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Установка пакета "Аналитика фабрики данных Azure" из Azure Marketplace

![Перейдите в Azure Marketplace, введите "Analytics Filter" и выберите "аналитика фабрики данных Azure (Предварительная версия)".](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Сведения о "аналитике фабрики данных Azure (Предварительная версия)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

Щелкните **создать** , а затем выберите **Рабочая область OMS** и **параметры рабочей области OMS**.

![Создание нового решения](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Мониторинг метрик фабрики данных

При установке службы "аналитика фабрики данных Azure" создается набор представлений по умолчанию, в результате чего становятся доступными следующие метрики.

- Запуски ADF — 1) запуски конвейеров фабрикой данных
 
- выполнение ADF: 2) Activity Runs by Data Factory;

- Запуски ADF-3) запуски триггеров фабрикой данных

- Ошибки ADF — 1) первые 10 ошибок конвейера по фабрике данных

- Ошибки ADF — 2) первые 10 запусков операций по фабрике данных

- Ошибки ADF-3) 10 основных ошибок триггера по фабрике данных

- Статистика ADF-1) выполняемые действия по типу

- Статистика ADF-2. запуски триггеров по типу

- Статистика ADF-3) длительность выполнения конвейера в максимальной

![Окно с выделенными книгами (Предварительная версия) и "Азуредатафакторяналитикс"](media/data-factory-monitor-oms/monitor-oms-image6.png)

Вы можете визуализировать предыдущие метрики, просматривать запросы, основанные на этих метриках, изменять запросы, создавать предупреждения и выполнять другие действия.

![Графическое представление выполнения конвейера по фабрике данных "](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Аналитика фабрики данных Azure (Предварительная версия) отправляет журналы диагностики в целевые таблицы для _конкретных ресурсов_ . Вы можете создавать запросы к следующим таблицам: _адфпипелинерун_, _адфтригжеррун_и _адфактивитирун_.

## <a name="alerts"></a>Оповещения

Войдите в портал Azure и выберите **мониторинг** > **предупреждения** , чтобы создать оповещения.

![Оповещения в меню портала](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Создание предупреждений

1. Нажмите кнопку **+ Новое правило генерации оповещений**, чтобы создать оповещение.

    ![Новое правило генерации оповещений](media/monitor-using-azure-monitor/alerts_image4.png)

1. Определите условие оповещения.

    > [!NOTE]
    > Убедитесь, что в раскрывающемся списке **Фильтровать по типу ресурсов** выбрано значение **все** .

    !["Определение условия оповещения" > "Выбор целевого объекта", который открывает область "Выбор ресурса" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Определение условия оповещения" > "добавить условие", которое открывает панель "Настройка логики сигнала"](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Панель «Настройка типа сигнала»](media/monitor-using-azure-monitor/alerts_image7.png)

1. Указание сведений для оповещения.

    ![Сведения об оповещении](media/monitor-using-azure-monitor/alerts_image8.png)

1. Определите группу действий.

    ![Создать правило с выделенным пунктом "Новая группа действий"](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Создание новой группы действий](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Настройка электронной почты, SMS, push-уведомлений и речи](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Определение группы действий](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Дальнейшие действия
[Программное отслеживание конвейеров и управление ими](monitor-programmatically.md)
