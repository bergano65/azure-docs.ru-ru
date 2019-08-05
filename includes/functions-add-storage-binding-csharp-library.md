---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 4db460a5dcefb49de3ad2b594d3957cbcf7445c3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592801"
---
В проекте библиотеки классов C# привязки определяются как атрибуты привязки в методе функции. Затем на основе этих атрибутов автоматически создается файл *function.json*.

Откройте файл проекта *HttpTrigger.cs* и добавьте следующий оператор `using`:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Добавьте следующий параметр в определение метода `Run`:

```cs
[Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
```

Параметр `msg` относится к типу `ICollector<T>`, который представляет собой коллекцию сообщений, записываемых в выходную привязку после завершения функции. В этом случае выходные данные представляют собой очередь хранилища с именем `outqueue`. Строка подключения к учетной записи хранения задана атрибутом `StorageAccountAttribute`. Он указывает параметр, который содержит строку подключения к учетной записи хранения и может применяться на уровне класса, метода или параметра. В этом случае вы можете опустить `StorageAccountAttribute`, так как уже используете учетную запись хранения по умолчанию.

Определение метода Run должно выглядеть следующим образом:  

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
```
