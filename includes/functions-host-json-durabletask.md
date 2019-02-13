---
title: включение файла
description: включение файла
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a3af711503445000d9613feb2eec7967442fe538
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736009"
---
Параметры конфигурации для [устойчивых функций](../articles/azure-functions/durable-functions-overview.md).

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

Имена центров задач должны начинаться с буквы и содержать только буквы и цифры. Если имя не указано, центру задач в приложении-функции назначается имя по умолчанию **DurableFunctionsHub**. Дополнительные сведения см. в статье о [центрах задач](../articles/azure-functions/durable-functions-task-hubs.md).

|Свойство  |значение по умолчанию | ОПИСАНИЕ |
|---------|---------|---------|
|HubName|DurableFunctionsHub|Альтернативные имена [центра задач](../articles/azure-functions/durable-functions-task-hubs.md) позволяют изолировать несколько приложений устойчивых функций друг от друга, даже если они используют один и тот же интерфейс хранилища.|
|ControlQueueBatchSize|32|Количество сообщений, одновременно извлекаемых из очереди управления.|
|PartitionCount |4.|Число разделов для очереди управления. Допускается целочисленное значение в диапазоне от 1 до 16.|
|ControlQueueVisibilityTimeout |5 мин|Время видимости для сообщений, выведенных из очереди управления.|
|WorkItemQueueVisibilityTimeout |5 мин|Время видимости для сообщений, выведенных из очереди рабочих элементов.|
|MaxConcurrentActivityFunctions |10× количество процессоров на текущем компьютере|Максимальное число функции действия, которые могут параллельно обрабатываться на одном экземпляре узла.|
|MaxConcurrentOrchestratorFunctions |10× количество процессоров на текущем компьютере|Максимальное количество функций оркестратора, которые могут быть обработаны параллельно в одном экземпляре узла.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|Имя параметра приложения, в котором хранится строка подключения к службе хранилища Azure для управления базовыми ресурсами этой службы.|
|TraceInputsAndOutputs |false|Это значение указывает, нужно ли отслеживать входы и выходы вызовов функций. При трассировке событий выполнения функции по умолчанию для вызовов функций фиксируется количество байтов в сериализованных входных и выходных данных. Это позволяет получить некоторое представление о входах и выходах, не увеличивая размеры журналов и не раскрывая в них конфиденциальные сведения. Если вы присвоите этому свойству значение true, в журналы выполнения функций будет включаться полное содержимое их входов и выходов.|
|LogReplayEvents|false|Значение, указывающее, следует ли записывать повторные события оркестрации в Application Insights.|
|EventGridTopicEndpoint ||URL-адрес конечной точки пользовательского раздела службы "Сетка событий Azure". Если установлен этот параметр, события уведомления о жизненном цикле оркестрации публикуются в указанную конечную точку. Это свойство поддерживает разрешение параметров приложения.|
|EventGridKeySettingName ||Имя параметра приложения, содержащего ключ для аутентификации в пользовательском разделе службы "Сетка событий Azure" в `EventGridTopicEndpoint`.|
|EventGridPublishRetryCount|0|Число повторных попыток, если публикация в разделе "Сетка событий" завершается сбоем.|
|EventGridPublishRetryInterval|5 мин|Интервал повторных попыток для публикации в разделе "Сетка событий" указывается в формате *чч: мм:сс*.|

Многие из них предназначены для оптимизации производительности. Дополнительные сведения см. в статье [о производительности и масштабируемости](../articles/azure-functions/durable-functions-perf-and-scale.md).