---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81791711"
---
### <a name="functions-2x-and-higher"></a>Функции 2. x и более поздних версий

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Свойство  |По умолчанию | Описание |
|---------|---------|---------|
|maxBatchSize|10|Максимальное число событий, получаемых в цикле получения.|
|prefetchCount|300|Счетчик предварительной выборки по умолчанию, используемый `EventProcessorHost`базовым объектом.|
|batchCheckpointFrequency|1|Количество пакетов событий, которые необходимо обработать перед созданием контрольной точки курсора EventHub.|

> [!NOTE]
> Ссылки на Host. JSON в функциях Azure 2. x и более поздних версий см. в [справочнике по Host. JSON для функций Azure](../articles/azure-functions/functions-host-json.md).

### <a name="functions-1x"></a>Функции 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Свойство  |По умолчанию | Описание |
|---------|---------|---------| 
|maxBatchSize|64|Максимальное число событий, получаемых в цикле получения.|
|prefetchCount|н/д|Предварительная выборка по умолчанию, которая будет использоваться базовым `EventProcessorHost`объектом.| 
|batchCheckpointFrequency|1|Количество пакетов событий, которые необходимо обработать перед созданием контрольной точки курсора EventHub.| 

> [!NOTE]
> Ссылку на Host. JSON в функциях Azure 1. x см. в [справочнике по Host. JSON для функций Azure 1. x](../articles/azure-functions/functions-host-json-v1.md).

