---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0a3f739bae3ec758c8e25d581d3e2b9feb4af5b1
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133740"
---
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
