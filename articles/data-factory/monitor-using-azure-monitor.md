---
title: Мониторинг фабрик данных с помощью Azure Monitor
description: Узнайте, как использовать Azure Monitor для мониторинга конвейеров /Azure Data Factory, включив диагностические журналы с информацией из Фабрики данных.
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
ms.openlocfilehash: 8325b4ef6b89a76eeec418386cec4922cb5916b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979146"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Оповещение и мониторинг фабрик данных с помощью Azure Monitor

Облачные приложения сложны и имеют много движущихся частей. Мониторы предоставляют данные, чтобы гарантировать, что ваши приложения остаются в состоянии работоспособного. Мониторы также помогут вам избежать потенциальных проблем и устранения прошлых неполадок.

Вы можете использовать данные мониторинга, чтобы получить глубокое представление о ваших приложениях. Эти знания помогают повысить производительность и удобство обслуживания приложений. Он также помогает автоматизировать действия, которые в противном случае требуют ручного вмешательства.

Azure Monitor предоставляет базовые метрики инфраструктуры и журналы для большинства служб Azure. Журналы диагностики Azure выдаются ресурсом. Они содержат подробные и своевременные данные о работе этого ресурса. А Azure Data Factory записывает диагностические журналы в Monitor.

Для получения подробной информации смотрите [обзор Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-data"></a>Хранение данных фабрики данных Azure

Data Factory хранит данные о проводах всего 45 дней. Используйте Monitor, если вы хотите сохранить эти данные в течение более длительного времени. С помощью Monitor можно направлять диагностические журналы для анализа. Вы также можете хранить их в учетной записи хранения, чтобы у вас была заводская информация на выбранный период времени.

## <a name="diagnostic-logs"></a>Журналы диагностики

* Сохраните свои диагностические журналы на учетную запись хранения для аудита или ручного осмотра. Можно использовать диагностические настройки, чтобы указать время хранения в течение нескольких дней.
* Поток журналов в концентраторы событий Azure. Эти журналы становятся вхотворными в партнерскую службу или в пользовательское аналитическое решение, такое как Power BI.
* Проанализируйте журналы с помощью log Analytics.

Можно использовать учетную запись хранения или пространство имен события-хаба, которое не входит в подписку ресурса, испускаемого журналами. Пользователь, который настраивает настройку, должен иметь соответствующий ролевой контроль доступа (RBAC) к обеим подпискам.

## <a name="set-up-diagnostic-logs"></a>Настройка журналов диагностики

### <a name="diagnostic-settings"></a>Параметры диагностики

Используйте диагностические настройки для настройки диагностических журналов для некомпьютерных ресурсов. Настройки управления ресурсами имеют следующие функции:

* В них указывается, куда отправляются диагностические журналы. Примеры включают учетную запись хранения Azure, концентратор событий Azure или журналы мониторов.
* В них указывается, какие категории журналов отправляются.
* В них указывается, как долго каждая категория журнала должна храниться в учетной записи хранилища.
* Срок хранения 0 дней означает, что журналы хранятся неограниченно долго. В противном случае значение может со от1 до 2 147 483 647.
* Если политики удержания установлены, но хранение журналов в учетной записи хранилища отключено, политики удержания не имеют эффекта. Например, это условие может произойти при выборе только вариантов журналов Event Hubs или Monitor.
* Политики удержания применяются в день. Граница между днями происходит в полночь скоординированное универсальное время (UTC). В конце дня удаляются журналы дней, выходящих за рамки политики удержания. Например, если у вас есть политика удержания одного дня, в начале сегодняшнего дня журналы из довчерашнего прошлого года удаляются.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Включить диагностические журналы через API Azure Monitor REST

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Создание или обновление настройки диагностики в API Monitor REST

##### <a name="request"></a>Запрос

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Заголовки

* Замените `{api-version}` на `2016-09-01`.
* Замените `{resource-id}` идентификатор ресурса, для которого необходимо отойти от диагностических настроек. Для получения дополнительной информации [см.](../azure-resource-manager/management/manage-resource-groups-portal.md)
* В качестве заголовка `Content-Type` установите `application/json`.
* Установите заголовок авторизации в веб-токен JSON, полученный из Active Directory Azure (Azure AD). Для получения дополнительной информации смотрите [запросы на аутентификации](../active-directory/develop/authentication-scenarios.md).

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

| Свойство | Тип | Описание |
| --- | --- | --- |
| **storageAccountId** |Строка | Идентификатор ресурса учетной записи хранилища, на который требуется отправить диагностические журналы. |
| **serviceBusRuleId** |Строка | Идентификатор правил обслуживания автобуса в пространстве имен сервис-автобуса, в котором вы хотите создать концентраторы событий для потоковой передачи диагностических журналов. Идентификатор `{service bus resource ID}/authorizationrules/{key name}`правила имеет формат .|
| **workspaceId** | Сложный тип | Массив метрических зерен времени и их политики удержания. Значение этого свойства пусто. |
|**Метрики**| Значения параметров выполнения конвейера, которые должны быть переданы в вызываемый конвейер.| Объект JSON, который отображает имена параметров для значений аргументов. |
| **Журналы**| Сложный тип| Название категории диагностического журнала для типа ресурса. Чтобы получить список категорий диагностического журнала для ресурса, выполните операцию диагностики GET. |
| **Категории**| Строка| Массив категорий журналов и политики их удержания. |
| **timeGrain** | Строка | Детализация метрик, которые запечатлены в формате продолжительности ISO 8601. Значение свойства должно `PT1M`быть, которое определяет одну минуту. |
| **Включен**| Логическое | Уточняется, включен ампибулия категории метрикили или журнала для этого ресурса. |
| **retentionPolicy**| Сложный тип| Описывает политику хранения метрики или категории журналов. Это свойство используется только для учетных записей хранения. |
|**Дней**| Int| Количество дней для сохранения метрик или журналов. Если значение свойства 0, журналы хранятся навсегда. Это свойство используется только для учетных записей хранения. |

##### <a name="response"></a>Ответ

200 OK.


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

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Получить информацию о настройках диагностики в Monitor REST API

##### <a name="request"></a>Запрос

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Заголовки

* Замените `{api-version}` на `2016-09-01`.
* Замените `{resource-id}` идентификатор ресурса, для которого необходимо отойти от диагностических настроек. Для получения дополнительной информации [см.](../azure-resource-manager/management/manage-resource-groups-portal.md)
* В качестве заголовка `Content-Type` установите `application/json`.
* Установите заголовок авторизации в веб-токен JSON, полученный из Azure AD. Для получения дополнительной информации смотрите [запросы на аутентификации](../active-directory/develop/authentication-scenarios.md).

##### <a name="response"></a>Ответ

200 OK.

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
Для получения дополнительной [информации см.](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs-and-events"></a>Схема журналов и событий

### <a name="monitor-schema"></a>Схема монитора

#### <a name="activity-run-log-attributes"></a>Атрибуты журнала, запущенные для активности

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

| Свойство | Тип | Описание | Пример |
| --- | --- | --- | --- |
| **Уровень** |Строка | Уровень диагностических журналов. Для журналов, управляемых деятельностью, установите значение свойства до 4. | `4` |
| **Correlationid** |Строка | Уникальный идентификатор для отслеживания конкретного запроса. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Время** | Строка | Время события в формате `YYYY-MM-DDTHH:MM:SS.00000Z`TIMEspan UTC . | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| Строка| Идентификатор выполнения действия. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| Строка| Идентификатор запуска конвейера. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| Строка | Идентификатор, связанный с ресурсом data-factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Категории**| Строка | Категория диагностических журналов. В качестве значения свойства задайте `ActivityRuns`. | `ActivityRuns` |
|**Уровень**| Строка | Уровень диагностических журналов. В качестве значения свойства задайте `Informational`. | `Informational` |
|**эксплуатацияИмя**| Строка | Название деятельности со своим статусом. Если действие является началом сердцебиения, `MyActivity -`значение свойства . Если действие является конечным сердцебиением, значение свойства `MyActivity - Succeeded`. | `MyActivity - Succeeded` |
|**pipelineName**| Строка | Название трубопровода. | `MyPipeline` |
|**activityName**| Строка | Имя действия. | `MyActivity` |
|**start**| Строка | Время начала действия выполняется в формате timespan UTC. | `2017-06-26T20:55:29.5007959Z`|
|**end**| Строка | Время окончания действия выполняется в формате TIMESpan UTC. Если журнал диагностики показывает, что действие началось, но `1601-01-01T00:00:00Z`еще не завершено, значение свойства находится в. | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Атрибуты журнала, запускаемые трубопроводом

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

| Свойство | Тип | Описание | Пример |
| --- | --- | --- | --- |
| **Уровень** |Строка | Уровень диагностических журналов. Для журналов, управляемых деятельностью, установите значение свойства до 4. | `4` |
| **Correlationid** |Строка | Уникальный идентификатор для отслеживания конкретного запроса. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Время** | Строка | Время события в формате `YYYY-MM-DDTHH:MM:SS.00000Z`TIMEspan UTC . | `2017-06-28T21:00:27.3534352Z` |
|**runId**| Строка| Идентификатор запуска конвейера. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| Строка | Идентификатор, связанный с ресурсом data-factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Категории**| Строка | Категория диагностических журналов. В качестве значения свойства задайте `PipelineRuns`. | `PipelineRuns` |
|**Уровень**| Строка | Уровень диагностических журналов. В качестве значения свойства задайте `Informational`. | `Informational` |
|**эксплуатацияИмя**| Строка | Название трубопровода вместе с его статусом. После завершения выполнения трубопровода значение `Pipeline - Succeeded`свойства находится в. | `MyPipeline - Succeeded`. |
|**pipelineName**| Строка | Название трубопровода. | `MyPipeline` |
|**start**| Строка | Время начала действия выполняется в формате timespan UTC. | `2017-06-26T20:55:29.5007959Z`. |
|**end**| Строка | Время окончания действия выполняется в формате TIMESpan UTC. Если журнал диагностики показывает, что действие началось, `1601-01-01T00:00:00Z`но еще не завершено, значение свойства находится в центре внимания.  | `2017-06-26T20:55:29.5007959Z` |
|**состояние**| Строка | Окончательный статус запуска конвейера. Возможные значения `Succeeded` свойств `Failed`есть и . | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Атрибуты журнала триггера

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

| Свойство | Тип | Описание | Пример |
| --- | --- | --- | --- |
| **Уровень** |Строка | Уровень диагностических журналов. Для журналов, управляемых деятельностью, установите значение свойства до 4. | `4` |
| **Correlationid** |Строка | Уникальный идентификатор для отслеживания конкретного запроса. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Время** | Строка | Время события в формате `YYYY-MM-DDTHH:MM:SS.00000Z`TIMEspan UTC . | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| Строка| Идентификатор триггера. | `08587023010602533858661257311` |
|**resourceId**| Строка | Идентификатор, связанный с ресурсом data-factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Категории**| Строка | Категория диагностических журналов. В качестве значения свойства задайте `PipelineRuns`. | `PipelineRuns` |
|**Уровень**| Строка | Уровень диагностических журналов. В качестве значения свойства задайте `Informational`. | `Informational` |
|**эксплуатацияИмя**| Строка | Имя триггера с его окончательным состоянием, которое указывает на то, успешно ли срабатывает триггер. Если сердцебиение было успешным, стоимость `MyTrigger - Succeeded`недвижимости. | `MyTrigger - Succeeded` |
|**триггер-имя**| Строка | Имя триггера. | `MyTrigger` |
|**триггерТип**| Строка | Тип триггера. Возможные значения `Manual Trigger` свойств `Schedule Trigger`есть и . | `ScheduleTrigger` |
|**triggerEvent**| Строка | Событие спускового крючка. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| Строка | Время начала пускового крючка в формате timespan UTC. | `2017-06-26T20:55:29.5007959Z`|
|**состояние**| Строка | Окончательный статус, показывающий, успешно ли срабатывает спусковой крючок. Возможные значения `Succeeded` свойств `Failed`есть и . | `Succeeded`|

### <a name="log-analytics-schema"></a>Схема аналитики журнала

Log Analytics наследует схему от Monitor со следующими исключениями:

* Первая буква в названии каждой колонки капитализирована. Например, название столбца "correlationId" в Monitor является "CorrelationId" в журнале Analytics.
* Там нет "Уровень" колонки.
* Динамический столбец "свойства" сохраняется в качестве следующего динамического типа blob JSON.

    | Колонка Azure Monitor | Колонка «Аналитика журнала» | Тип |
    | --- | --- | --- |
    | $.properties. UserProperties | UserProperties | Динамический |
    | $.properties. Аннотации | Заметки | Динамический |
    | $.properties. Вход | Входные данные | Динамический |
    | $.properties. Выход | Выходные данные | Динамический |
    | $.properties. Код error.error | ErrorCode | INT |
    | $.properties. Error.message | ErrorMessage | строка |
    | $.properties. Ошибка | Error | Динамический |
    | $.properties. Предшественников | Предшественников | Динамический |
    | $.properties. Параметры | Параметры | Динамический |
    | $.properties. Системные параметры | SystemParameters | Динамический |
    | $.properties. Теги | Теги | Динамический |
    
## <a name="metrics"></a>Метрики

С помощью Monitor можно получить представление о производительности и работоспособности рабочих нагрузок Azure. Наиболее важным типом данных Монитора является метрика, которая также называется счетчиком производительности. Метрики излучаются большинством ресурсов Azure. Monitor предоставляет несколько способов настройки и потребления этих показателей для мониторинга и устранения неполадок.

Версия 2 Azure Data Factory издает следующие метрики.

| **Метрические**           | **Имя метрического дисплея**         | **Единица измерения** | **Тип агрегации** | **Описание**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Метрики успешных выполнений конвейеров. | Count    | Итог                | Общее количество запусков трубопровода, которое удалось вывести за минуту. |
| PipelineFailedRuns   | Метрики неудачных выполнений конвейеров.    | Count    | Итог                | Общее количество запусков конвейера, которые не удалось в течение минутного окна.    |
| ActivitySucceededRuns | Метрики успешных выполнений действий. | Count    | Итог                | Общее количество выполняется, что удалось в течение минутного окна.  |
| ActivityFailedRuns   | Метрики неудачных выполнений действий.    | Count    | Итог                | Общее количество выполняется в течение минутного окна.     |
| TriggerSucceededRuns | Метрики успешных запусков триггеров.  | Count    | Итог                | Общее количество триггерных запусков, которые удалось вызвать в течение минутного окна.   |
| TriggerFailedRuns    | Метрики неудачных запусков триггеров.     | Count    | Итог                | Общее количество триггерных запусков, которые не удалось в течение минуты окна.      |

Чтобы получить доступ к метрикам, заполните инструкции в [платформе данных Azure Monitor.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)

> [!NOTE]
> Излучаются только завершенные, срабатывающие действия и события запусков конвейера. В прогрессе и песочнице / отладка работает **не** излучаются. 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Мониторинг метрик фабрики данных с помощью Azure Monitor

Для маршрутизатоватьданных данных на Monitor можно использовать интеграцию Data Factory с Monitor. Это удобно в следующих случаях:

* Вы хотите написать сложные запросы на богатом наборе метрик, который публикуется Data Factory для мониторинга. Вы можете создавать пользовательские оповещения на этих запросах через Monitor.

* Требуется отслеживать фабрики данных. Данные можно направлять с нескольких фабрик данных в одно рабочее пространство монитора.

Уделите 7 минут своего времени, чтобы просмотреть следующее видео с кратким обзором и демонстрацией этой функции.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Настройка диагностических настроек и рабочего пространства

Создайте или добавьте диагностические настройки для фабрики данных.

1. На портале перейдите на Monitor. Выберите **настройки настройки диагностики.** > **Diagnostic settings**

1. Выберите фабрику данных, для которой требуется установить диагностическую настройку.

1. Если на выбранной фабрике данных нет настроек, вам предлагается создать настройку. Выберите **Включить диагностику**.

   ![Создание настройки диагностики, если не существует параметров](media/data-factory-monitor-oms/monitor-oms-image1.png)

   При наличии существующих параметров на фабрике данных можно увидеть список настроек, уже настроенных на фабрике данных. Выберите **Диагностическую настройку добавления.**

   ![Добавление настройки диагностики при наличии настроек](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Назовите настройку имени, выберите **Send to Log Analytics,** а затем выберите рабочее пространство из **рабочего пространства Log Analytics.**

    ![Назовите настройки и выберите рабочее пространство для анализа журналов](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. Нажмите кнопку **Сохранить**.

Через несколько мгновений новая настройка появляется в списке настроек для этой фабрики данных. Диагностические журналы передаются в рабочее пространство, как только генерируются новые данные о событиях. Между тем, когда событие испускается, и когда оно появляется в журнале Analytics, может проходить до 15 минут.

* В режиме _ресурсо-специфические_ журналы из Azure Data Factory перетекают в таблицы _ADFPipelineRun,_ _ADFTriggerRun_и _ADFActivityRun_
* В режиме _Azure-Diagnostics_ журналы диагностики поступают в таблицу _AzureDiagnostics_.

> [!NOTE]
> Поскольку таблица журналов Azure не может иметь более 500 столбцов, мы настоятельно рекомендуем выбрать режим ресурсно-специфический ресурс. Для получения дополнительной информации, см [Журнал Аналитика Известные ограничения](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Установка пакета "Аналитика фабрики данных Azure" из Azure Marketplace

![Перейдите на "Azure Marketplace", введите фильтр "Аналитика" и выберите "Аналитика фабрики данных Azure (Preview")](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Подробная информация о "Аналитика фабрики данных Azure (Предварительный просмотр)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

Выберите **Создать,** а затем выберите параметры **рабочего пространства OMS** и **OMS Workspace.**

![Создание нового решения](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Метрики фабрики мониторинга данных

Установка Аналитики фабрики данных Azure создает набор представлений по умолчанию, чтобы включили следующие метрики:

- ADF запускает - 1) Трубопровод работает по фабрике данных
 
- выполнение ADF: 2) Activity Runs by Data Factory;

- ADF запускает - 3) Триггер работает по фабрике данных

- Ошибки ADF - 1) Топ 10 Ошибки трубопровода по фабрике данных

- Ошибки ADF - 2) Топ 10 активность выполняется фабрикой данных

- Ошибки ADF - 3) Топ 10 Триггер Ошибки по фабрике данных

- Статистика ADF - 1) Активность выполняется по типу

- Статистика ADF - 2) Триггер работает по типу

- Статистика ADF - 3) Макс Трубопровод работает Продолжительность

![Окно с "Рабочие книги (Предварительный просмотр)" и "AzureDataFactoryAnalytics" выделены](media/data-factory-monitor-oms/monitor-oms-image6.png)

Вы можете визуализировать предыдущие метрики, посмотреть запросы, лежащие в основе этих метрик, отсеивать запросы, создавать оповещения и предпринимать другие действия.

![Графическое представление конвейера, протекаемого фабрикой данных"](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Аналитика фабрики данных Azure (Preview) отправляет диагностические журналы в таблицы _назначения, специфичные для ресурсов._ Вы можете писать запросы по следующим таблицам: _ADFPipelineRun_, _ADFTriggerRun_и _ADFActivityRun._

## <a name="alerts"></a>видны узлы

Вопийте на порталaz и выберите **оповещения монитора** > **Alerts** для создания оповещений.

![Оповещения в меню портала](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Создание предупреждений

1. Нажмите кнопку **+ Новое правило генерации оповещений**, чтобы создать оповещение.

    ![Новое правило генерации оповещений](media/monitor-using-azure-monitor/alerts_image4.png)

1. Определите состояние оповещения.

    > [!NOTE]
    > Убедитесь в том, чтобы выбрать **все** в **фильтре по типу ресурсов** выпадающих вниз списка.

    !["Определить состояние оповещения" > "Выбрать цель", которая открывает панель "Выбрать ресурс" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Определить состояние оповещения" >" Добавить критерии", которая открывает "Настройка логики сигнала" панели](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Панель "Настройка типа сигнала"](media/monitor-using-azure-monitor/alerts_image7.png)

1. Указание сведений для оповещения.

    ![Сведения об оповещении](media/monitor-using-azure-monitor/alerts_image8.png)

1. Определите группу действий.

    ![Создать правило с выделенной «Новой группой действий»](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Создание новой группы действий](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Настройка электронной почты, SMS, толчок, и голос](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Определение группы действий](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Дальнейшие действия
[Мониторинг и управление трубопроводами программно](monitor-programmatically.md)
