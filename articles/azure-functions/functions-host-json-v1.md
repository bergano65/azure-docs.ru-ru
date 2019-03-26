---
title: Справочник по файлу host.json для службы "Функции Azure" версии 1.x
description: Справочная документация по файлу host.json для Функций Azure в среде выполнения версии 1.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: glenga
ms.openlocfilehash: 44bc5a245d1bcbc8ff53991af4193ef86f7cd704
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436325"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>Справочник по файлу host.json для службы "Функции Azure" версии 1.x

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Версия 1](functions-host-json-v1.md)
> * [Версия 2](functions-host-json.md)

Файл метаданных *host.json* содержит параметры глобальной конфигурации, влияющие на все функции приложения-функции. В этой статье перечислены параметры, которые доступны для среды выполнения версии 1. Схему JSON можно найти по адресу http://json.schemastore.org/host.

> [!NOTE]
> Эта статья предназначена для службы "Функции Azure" версии 1.x.  Сведения о файле host.json в Функциях Azure версии 2.x см. в [этой статье](functions-host-json.md).

В [параметрах приложения](functions-app-settings.md) можно управлять другими настройками приложения-функции.

Некоторые параметры host.json используются только при локальном запуске в файле [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="sample-hostjson-file"></a>Пример файла host.json

В приведенном ниже примере файлов *host.json* указаны все возможные параметры.


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

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Параметры конфигурации для [триггеров и привязок концентратора событий](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

Список функций, которые выполняют узел заданий. Пустой массив означает выполнение всех функций. Предназначен для использования только при [локальном выполнении](functions-run-local.md). Чтобы отключить определенные функции в приложениях-функциях Azure, выполните следующие действия в разделе [Способы отключения функций в решении "Функции Azure"](disable-function.md) вместо использования этого параметра.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Указывает время ожидания для всех функций. В бессерверных планах потребления допускается диапазон от 1 секунды до 10 минут, а значение по умолчанию — 5 минут. План службы приложений не имеет общих ограничений, а значение по умолчанию зависит от версии среды выполнения.

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

Параметры конфигурации для [триггеров и привязок http](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*Только версия 1.x.*

Уникальный идентификатор для узла заданий. Это может быть глобальный уникальный идентификатор (GUID), вводимый в нижнем регистре с удаленными дефисами. Необходим при локальном выполнении. При работе в Azure, мы рекомендуем не задавать значение идентификатора. Идентификатор создается автоматически в Azure, когда `id` пропускается. 

Если вы предоставляете общий доступ к учетной записи хранения для нескольких приложений-функций, убедитесь, что у всех них разные `id`. Можно опустить свойство `id` или вручную задать для `id` всех приложений-функций разные значения. Для триггера таймера используется блокировка хранилища. Это гарантирует наличие только одного экземпляра таймера, когда приложение-функция масштабируется до нескольких экземпляров. Если у двух приложений-функций один `id` и для каждого используется триггер таймера, запустится только один таймер.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

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

## <a name="queues"></a>queues

Параметры конфигурации для [триггеров и привязок очереди хранилища](functions-bindings-storage-queue.md).

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Свойство  |значение по умолчанию | ОПИСАНИЕ |
|---------|---------|---------| 
|maxPollingInterval|60 000|Максимальный интервал в миллисекундах между опросами очереди.| 
|visibilityTimeout|0|Интервал времени между повторными попытками, когда при обработке сообщения возникает сбой.| 
|batchSize|16|Количество сообщений очереди, которые среда выполнения функций одновременно получает и обрабатывает в параллельном режиме. Когда число обрабатываемых сообщений достигает `newBatchThreshold`, среда выполнения получает следующий пакет и начинает обработку содержащихся в нем сообщений. Поэтому максимальное количество сообщений, одновременно обрабатываемых каждой функцией, равно `batchSize` плюс `newBatchThreshold`. Это ограничение применяется отдельно к каждой функции, активируемой с помощью очереди. <br><br>Если вы не хотите, чтобы сообщения из одной очереди обрабатывались параллельно, можно установить для `batchSize` значение 1. Тем не менее этот параметр позволяет исключить параллелизм только при условии, что приложение-функция выполняется на одной виртуальной машине. Если приложение-функция развернуто на нескольких виртуальных машинах, каждая машина может запускать один экземпляр каждой функции, активируемой с помощью очереди.<br><br>Максимальное значение `batchSize` — 32. | 
|maxDequeueCount|5|Число повторных попыток обработки сообщения, прежде чем поместить его в очередь подозрительных сообщений.| 
|newBatchThreshold|batchSize/2|Каждый раз, когда количество сообщений, обрабатываемых параллельно, достигает этого числа, среда выполнения получает другой пакет.| 

## <a name="servicebus"></a>serviceBus

Параметр конфигурации для [триггеров и привязок служебной шины](functions-bindings-service-bus.md).

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Свойство  |значение по умолчанию | ОПИСАНИЕ |
|---------|---------|---------| 
|maxConcurrentCalls|16|Максимальное число параллельных вызовов к обратному вызову, которое должен инициировать процесс обработки сообщений. По умолчанию в среде выполнения службы "Функции" одновременно обрабатывается несколько сообщений очереди. Чтобы среда выполнения обрабатывала в любой момент времени только одно сообщение очереди или раздела, для свойства `maxConcurrentCalls` нужно задать значение 1. | 
|prefetchCount|Недоступно|Значение PrefetchCount по умолчанию, которое будет использоваться базовым компонентом MessageReceiver.| 
|autoRenewTimeout|00:05:00|Максимальный период времени, в течение которого блокировка сообщения будет продлеваться автоматически.| 

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

Параметры конфигурации для журналов, создаваемых с помощью объекта `TraceWriter`. Ознакомьтесь с разделами, посвященными ведению журналов, для [C#](functions-reference-csharp.md#logging) и [Node.js](functions-reference-node.md#writing-trace-output-to-the-console).

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

## <a name="watchdirectories"></a>watchDirectories

Набор [каталогов общего кода](functions-reference-csharp.md#watched-directories), изменения которого должны отслеживаться.  Гарантирует, что если код в этих каталогах изменится, то эти изменения будут применены вашими функциями.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Узнайте, как обновить файл host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Ознакомьтесь с глобальными параметрами в переменных среды](functions-app-settings.md)
