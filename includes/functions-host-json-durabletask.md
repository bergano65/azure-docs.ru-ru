---
title: включить файл
description: включить файл
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6bb59db4c1b31033b1e116742dedc94621b1c60d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117056"
---
Параметры конфигурации для [устойчивых функций](../articles/azure-functions/durable-functions-overview.md).

### <a name="durable-functions-1x"></a>Долгосрочные функции 1.x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Долгосрочные функции 2.x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useGracefulShutdown": false
  }
  }
}

```

Имена центров задач должны начинаться с буквы и содержать только буквы и цифры. Если имя не указано, центру задач в приложении-функции назначается имя по умолчанию **DurableFunctionsHub**. Дополнительные сведения см. в статье о [центрах задач](../articles/azure-functions/durable-functions-task-hubs.md).

|Свойство  |Значение по умолчанию | Описание |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Альтернативные имена [центра задач](../articles/azure-functions/durable-functions-task-hubs.md) позволяют изолировать несколько приложений устойчивых функций друг от друга, даже если они используют один и тот же интерфейс хранилища.|
|контрольКуиТБачгабизат|32|Количество сообщений, одновременно извлекаемых из очереди управления.|
|контрольКиКеБафферЕДПорог|256|Количество сообщений очереди управления, которые могут быть буферизированы в памяти в то время, в этот момент диспетчер будет ждать, прежде чем dequeuing каких-либо дополнительных сообщений.|
|partitionCount |4|Число разделов для очереди управления. Допускается целочисленное значение в диапазоне от 1 до 16.|
|контрольКиВидимостиTimeout |5 мин|Время видимости для сообщений, выведенных из очереди управления.|
|workItemQueueВидимостиВремя |5 мин|Время видимости для сообщений, выведенных из очереди рабочих элементов.|
|maxConcurrentActivityФункциИ |10× количество процессоров на текущем компьютере|Максимальное число функции действия, которые могут параллельно обрабатываться на одном экземпляре узла.|
|maxConcurrentOrchestratorФункции |10× количество процессоров на текущем компьютере|Максимальное количество функций оркестратора, которые могут быть обработаны параллельно в одном экземпляре узла.|
|maxQueueОблинеЛ.»|30 секунд|Максимальный интервал обработки очереди управления и рабочей элемента в формате *hh:mm:ss.* Более высокие значения могут привести к более высокой обработке сообщений. Более низкие значения могут привести к увеличению затрат на хранение из-за увеличения транзакций хранения.|
|лазурныйStorageConnectionStringName |AzureWebJobsStorage|Имя параметра приложения, в котором хранится строка подключения к службе хранилища Azure для управления базовыми ресурсами этой службы.|
|отслеживаниеStoreConnectionStringName||Название строки соединения для использования для таблиц «История» и «Инстанции». Если не указано, `azureStorageConnectionStringName` используется соединение.|
|отслеживаниеStoreNamePrefix||Приставка для использования для таблиц «История» и «Инстанции» при `trackingStoreConnectionStringName` указании. Если не установить, значение префикса по умолчанию будет `DurableTask`. Если `trackingStoreConnectionStringName` не указано, то таблицы History `hubName` and Instances будут использовать значение `trackingStoreNamePrefix` в качестве префикса, и любой параметр будет проигнорирован.|
|traceInputsAndOutputs |false|Это значение указывает, нужно ли отслеживать входы и выходы вызовов функций. При трассировке событий выполнения функции по умолчанию для вызовов функций фиксируется количество байтов в сериализованных входных и выходных данных. Это поведение предоставляет минимальную информацию о том, как выглядят входные данные и выходы без вздутия бревна или непреднамеренного раскрытия конфиденциальной информации. Если вы присвоите этому свойству значение true, в журналы выполнения функций будет включаться полное содержимое их входов и выходов.|
|logReplayСобытия|false|Значение, указывающее, следует ли записывать повторные события оркестрации в Application Insights.|
|eventGridTopicEndpoint ||URL-адрес конечной точки пользовательского раздела службы "Сетка событий Azure". При установке этого свойства события уведомления о оркестровке жизненного цикла публикуются в этой конечную точку. Это свойство поддерживает разрешение параметров приложения.|
|eventGridKeySettingName ||Имя параметра приложения, содержащего ключ для аутентификации в пользовательском разделе службы "Сетка событий Azure" в `EventGridTopicEndpoint`.|
|eventGridPublishRetryCount|0|Число повторных попыток, если публикация в разделе "Сетка событий" завершается сбоем.|
|eventGridPublishRetryInterval|5 мин|Интервал повторных попыток для публикации в разделе "Сетка событий" указывается в формате *чч: мм:сс*.|
|eventGridPublishEventTypes||Список типов событий для публикации в Event Grid. Если не указано, все типы событий будут опубликованы. Разрешенные значения `Started` `Completed`включают, , `Failed`. `Terminated`|
|useGracefulShutdown|false|(Предварительный просмотр) Включить изящное завершение, чтобы уменьшить вероятность сбоя выполнения функций в процессе.|

Многие из этих параметров для оптимизации производительности. Дополнительные сведения см. в статье [о производительности и масштабируемости](../articles/azure-functions/durable-functions-perf-and-scale.md).
