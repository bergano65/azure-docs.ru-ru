---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66131708"
---
```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Свойство  |значение по умолчанию | Описание |
|---------|---------|---------| 
|maxBatchSize|64|Максимальное число событий, получаемых в цикле получения.|
|prefetchCount|Недоступно|Значение PrefetchCount по умолчанию, которое будет использоваться базовым узлом EventProcessorHost.| 
|batchCheckpointFrequency|1|Количество пакетов событий, которые необходимо обработать перед созданием контрольной точки курсора EventHub.| 
