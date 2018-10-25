---
title: Справочник по файлу host.json для Функций Azure
description: Справочная документация по файлу host.json для Функций Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: 704a41ec840e2a252a1bbb5c20688f722bd0cdfd
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887042"
---
# <a name="hostjson-reference-for-azure-functions"></a>Справочник по файлу host.json для Функций Azure

Файл метаданных *host.json* содержит параметры глобальной конфигурации, влияющие на все функции приложения-функции. В этой статье перечислены параметры, которые доступны. Схему JSON можно найти по адресу http://json.schemastore.org/host.

> [!NOTE]
> Существуют значительные различия в *host.json* между версиями v1 и v2 среды выполнения функций Azure. `"version": "2.0"` является обязательным для приложения-функции, предназначенного для среды выполнения v2.

В [параметрах приложения](functions-app-settings.md) можно управлять другими настройками приложения-функции.

Некоторые параметры host.json используются только при локальном запуске в файле [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="sample-hostjson-file"></a>Пример файла host.json

В приведенном ниже примере файлов *host.json* указаны все возможные параметры.

### <a name="version-2x"></a>Версия 2.x

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "eventHubs": {
          "maxBatchSize": 64,
          "prefetchCount": 256,
          "batchCheckpointFrequency": 1
        },
        "http": {
            "routePrefix": "api",
            "maxConcurrentRequests": 100,
            "maxOutstandingRequests": 30
        },
        "queues": {
            "visibilityTimeout": "00:00:10",
            "maxDequeueCount": 3
        },
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        },
        "serviceBus": {
          "maxConcurrentCalls": 16,
          "prefetchCount": 100,
          "autoRenewTimeout": "00:05:00"
        }
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "sampling": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 5
            }
        }
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

### <a name="version-1x"></a>Версия 1.x

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

В следующих разделах этой статьи объясняется каждое свойство верхнего уровня. Все они являются необязательными, если не указано иное.

## <a name="aggregator"></a>aggregator

Указывает, сколько вызовов функций обрабатывается при [расчете метрик для Application Insights](functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Свойство |значение по умолчанию  | ОПИСАНИЕ |
|---------|---------|---------| 
|batchSize|1000|Максимальное количество запросов, которое необходимо обработать.| 
|flushTimeout|00:00:30|Максимальный период времени, который необходимо обработать.| 

Вызовы функций обрабатываются, когда достигается одно из этих двух ограничений.

## <a name="applicationinsights"></a>applicationInsights

Управляет [функцией выборки в Application Insights](functions-monitoring.md#configure-sampling). В версии 2.x, этот параметр является дочерним элементом [ведения журнала](#log).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Свойство  |значение по умолчанию | ОПИСАНИЕ |
|---------|---------|---------| 
|isEnabled|Да|Включает или отключает выборку.| 
|maxTelemetryItemsPerSecond|5|Пороговое значение, при котором начинается выборка.| 

## <a name="durabletask"></a>durableTask

Параметры конфигурации для [устойчивых функций](durable-functions-overview.md).

```json
{
  "durableTask": {
    "HubName": "MyTaskHub",
    "ControlQueueBatchSize": 32,
    "PartitionCount": 4,
    "ControlQueueVisibilityTimeout": "00:05:00",
    "WorkItemQueueVisibilityTimeout": "00:05:00",
    "MaxConcurrentActivityFunctions": 10,
    "MaxConcurrentOrchestratorFunctions": 10,
    "AzureStorageConnectionStringName": "AzureWebJobsStorage",
    "TraceInputsAndOutputs": false,
    "LogReplayEvents": false,
    "EventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "EventGridKeySettingName":  "EventGridKey",
    "EventGridPublishRetryCount": 3,
    "EventGridPublishRetryInterval": "00:00:30"
  }
}
```

Имена центров задач должны начинаться с буквы и содержать только буквы и цифры. Если имя не указано, центру задач в приложении-функции назначается имя по умолчанию **DurableFunctionsHub**. Дополнительные сведения см. в статье о [центрах задач](durable-functions-task-hubs.md).

|Свойство  |значение по умолчанию | ОПИСАНИЕ |
|---------|---------|---------|
|HubName|DurableFunctionsHub|Альтернативные имена [центра задач](durable-functions-task-hubs.md) позволяют изолировать несколько приложений устойчивых функций друг от друга, даже если они используют один и тот же интерфейс хранилища.|
|ControlQueueBatchSize|32|Количество сообщений, одновременно извлекаемых из очереди управления.|
|PartitionCount |4.|Число разделов для очереди управления. Допускается целочисленное значение в диапазоне от 1 до 16.|
|ControlQueueVisibilityTimeout |5 мин|Время видимости для сообщений, выведенных из очереди управления.|
|WorkItemQueueVisibilityTimeout |5 мин|Время видимости для сообщений, выведенных из очереди рабочих элементов.|
|MaxConcurrentActivityFunctions |10× количество процессоров на текущем компьютере|Максимальное число функции действия, которые могут параллельно обрабатываться на одном экземпляре узла.|
|MaxConcurrentOrchestratorFunctions |10× количество процессоров на текущем компьютере|Максимальное число функции действия, которые могут параллельно обрабатываться на одном экземпляре узла.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|Имя параметра приложения, в котором хранится строка подключения к службе хранилища Azure для управления базовыми ресурсами этой службы.|
|TraceInputsAndOutputs |false|Это значение указывает, нужно ли отслеживать входы и выходы вызовов функций. При трассировке событий выполнения функции по умолчанию для вызовов функций фиксируется количество байтов в сериализованных входных и выходных данных. Это позволяет получить некоторое представление о входах и выходах, не увеличивая размеры журналов и не раскрывая в них конфиденциальные сведения. Если вы присвоите этому свойству значение true, в журналы выполнения функций будет включаться полное содержимое их входов и выходов.|
|LogReplayEvents|false|Значение, указывающее, следует ли записывать повторные события оркестрации в Application Insights.|
|EventGridTopicEndpoint ||URL-адрес конечной точки пользовательского раздела службы "Сетка событий Azure". Если установлен этот параметр, события уведомления о жизненном цикле оркестрации публикуются в указанную конечную точку. Это свойство поддерживает разрешение параметров приложения.|
|EventGridKeySettingName ||Имя параметра приложения, содержащего ключ для аутентификации в пользовательском разделе службы "Сетка событий Azure" в `EventGridTopicEndpoint`.|
|EventGridPublishRetryCount|0|Число повторных попыток, если публикация в разделе "Сетка событий" завершается сбоем.|
|EventGridPublishRetryInterval|5 мин|Интервал повторных попыток для публикации в разделе "Сетка событий" указывается в формате *чч: мм:сс*.|

Многие из них предназначены для оптимизации производительности. Дополнительные сведения см. в статье [о производительности и масштабируемости](durable-functions-perf-and-scale.md).

## <a name="eventhub"></a>eventHub

Параметры конфигурации для [триггеров и привязок концентратора событий](functions-bindings-event-hubs.md). В версии 2.x он является дочерним элементом [расширений](#extensions).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="extensions"></a>extensions

*Только версия 2.x.*

Свойство, которое возвращает объект, содержащий все параметры определенной привязки, такие как [http](#http) и [eventHub](#eventhub).

## <a name="functions"></a>functions

Список функций, которые выполняют узел заданий. Пустой массив означает выполнение всех функций. Предназначен для использования только при [локальном выполнении](functions-run-local.md). Чтобы отключить определенные функции в приложениях-функциях Azure, выполните следующие действия в разделе [Способы отключения функций в решении "Функции Azure"](disable-function.md) вместо использования этого параметра.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Указывает время ожидания для всех функций. В бессерверных планах потребления допускается диапазон от 1 секунды до 10 минут, а значение по умолчанию — 5 минут. План службы приложений не имеет общих ограничений, а значение по умолчанию зависит от версии среды выполнения. В версии 2.x значение по умолчанию для плана службы приложений составляет 30 минут. В версии 1.x это значение *null*, что означает отсутствие времени ожидания.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Параметры конфигурации для [монитора работоспособности узла](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Свойство  |значение по умолчанию | ОПИСАНИЕ |
|---------|---------|---------| 
|Включено|Да|Указывает, включена ли функция. | 
|healthCheckInterval|10 с|Интервал времени между периодическими фоновыми проверками работоспособности. | 
|healthCheckWindow|2 минуты|Скользящее окно времени, используемое в сочетании с параметром `healthCheckThreshold`.| 
|healthCheckThreshold|6|Максимальное количество попыток проверки работоспособности, которые могут завершиться сбоем, прежде чем инициируется повторный запуск.| 
|counterThreshold|0,80|Пороговое значение, при достижении которого счетчик производительности будет считаться неработоспособным.| 

## <a name="http"></a>http

Параметры конфигурации для [триггеров и привязок http](functions-bindings-http-webhook.md). В версии 2.x он является дочерним элементом [расширений](#extensions).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*Только версия 1.x.*

Уникальный идентификатор для узла заданий. Это может быть глобальный уникальный идентификатор (GUID), вводимый в нижнем регистре с удаленными дефисами. Необходим при локальном выполнении. При работе в Azure, мы рекомендуем не задавать значение идентификатора. Идентификатор создается автоматически в Azure, когда `id` пропускается. Невозможно установить пользовательский идентификатор приложения-функции при использовании среды выполнения версии 2.x.

Если вы предоставляете общий доступ к учетной записи хранения для нескольких приложений-функций, убедитесь, что у всех них разные `id`. Можно опустить свойство `id` или вручную задать для `id` всех приложений-функций разные значения. Для триггера таймера используется блокировка хранилища. Это гарантирует наличие только одного экземпляра таймера, когда приложение-функция масштабируется до нескольких экземпляров. Если у двух приложений-функций один `id` и для каждого используется триггер таймера, запустится только один таймер.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

*Только для версии 1.x; для версии 2.x используйте [ведение журнала](#logging).*

Управляет фильтрацией журналов, которые создаются [объектом ILogger](functions-monitoring.md#write-logs-in-c-functions) или [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Свойство  |значение по умолчанию | ОПИСАНИЕ |
|---------|---------|---------| 
|categoryFilter|Недоступно|Указывает параметры фильтрации по категории.| 
|defaultLevel|Информация|Для любых категорий, не указанных в массиве `categoryLevels`, отправляет журналы на этом уровне и выше в Application Insights.| 
|categoryLevels|Недоступно|Массив категорий, который определяет минимальный уровень ведения журнала для отправки в Application Insights для каждой категории. Указанная здесь категория управляет всеми категориями, которые начинаются с одного значения, при этом более длинные значения имеют приоритет. В предыдущем примере файла *host.json* все категории, которые начинаются с "Host.Aggregator", записываются в журнал на уровне `Information`. Все прочие категории, которые начинаются с "Host" (такие как "Host.Executor"), записываются в журнал на уровне `Error`.| 

## <a name="logging"></a>Ведение журналов

*Только для версии 2.x; для версии 1.x используйте [ведение журнала](#logger).*

Управляет поведением ведения журнала приложения-функции, включая Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "applicationInsights": {
        ...
    }
}
```

|Свойство  |значение по умолчанию | ОПИСАНИЕ |
|---------|---------|---------|
|fileLoggingMode|информационный.|В Application Insights отправляются журналы этого уровня и выше. |
|LogLevel|Недоступно|Объект, который определяет фильтрацию категорий журналов для функций в приложении. Версия 2.x соответствует макету ASP.NET Core для фильтрации категорий журналов. Это позволяет фильтровать ведения журнала определенных функций. Дополнительные сведения см. в документации по использованию ASP.NET Core [Фильтрация журнала](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). |
|applicationInsights|Недоступно| Параметр [applicationInsights](#applicationinsights). |

## <a name="queues"></a>queues

Параметры конфигурации для [триггеров и привязок очереди хранилища](functions-bindings-storage-queue.md). В версии 2.x он является дочерним элементом [расширений](#extensions).

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="servicebus"></a>serviceBus

Параметр конфигурации для [триггеров и привязок служебной шины](functions-bindings-service-bus.md). В версии 2.x он является дочерним элементом [расширений](#extensions).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>singleton

Параметры конфигурации для одноэлементной блокировки. Дополнительные сведения см. в [проблеме, посвященной одноэлементной блокировке, на портале GitHub](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Свойство  |значение по умолчанию | ОПИСАНИЕ |
|---------|---------|---------| 
|lockPeriod|00:00:15|Период времени, на который применяются блокировки уровня функции. Эти блокировки возобновляются автоматически.| 
|listenerLockPeriod|00:01:00|Период времени, на который применяются блокировки прослушивателя.| 
|listenerLockRecoveryPollingInterval|00:01:00|Интервал времени, используемый для восстановления блокировки прослушивателя, если блокировку прослушивателя не удалось получить при запуске.| 
|lockAcquisitionTimeout|00:01:00|Максимальный период времени, за который среда выполнения будет пытаться получить блокировку.| 
|lockAcquisitionPollingInterval|Недоступно|Интервал между попытками получения блокировки.| 

## <a name="tracing"></a>tracing

*Версия 1.x*

Параметры конфигурации для журналов, создаваемых с помощью объекта `TraceWriter`. Ознакомьтесь с разделами, посвященными ведению журналов, для [C#](functions-reference-csharp.md#logging) и [Node.js](functions-reference-node.md#writing-trace-output-to-the-console). В версии 2.x все поведения журнала контролируются [ведением журнала](#logging).

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Свойство  |значение по умолчанию | ОПИСАНИЕ |
|---------|---------|---------| 
|consoleLevel|info|Уровень трассировки для ведения журнала консоли. Доступны следующие параметры: `off`, `error`, `warning`, `info` и `verbose`.|
|fileLoggingMode|debugOnly|Уровень трассировки для ведения журнала файлов. Доступны следующие параметры: `never`, `always` и `debugOnly`.| 

## <a name="version"></a>версия

*Версия 2.x*

Строка версии `"version": "2.0"` является обязательной для приложения-функции, предназначенного для среды выполнения v2.

## <a name="watchdirectories"></a>watchDirectories

Набор [каталогов общего кода](functions-reference-csharp.md#watched-directories), изменения которого должны отслеживаться.  Гарантирует, что если код в этих каталогах изменится, то эти изменения будут применены вашими функциями.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Узнайте, как обновить файл host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Ознакомьтесь с глобальными параметрами в переменных среды](functions-app-settings.md)
