---
title: Центры задач в устойчивых функциях — Azure
description: Сведения о том, что такое центр задач в расширении устойчивых функций для Функций Azure. Learn how to configure task hubs.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 38c7da8a1de57ed5acf3248fc6a71431de0bd1e2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232794"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Центры задач в устойчивых функциях (Функции Azure)

*Центр задач* в [устойчивых функциях](durable-functions-overview.md) — это логический контейнер для ресурсов службы хранилища Azure, которые используются для оркестрации. Функции оркестраторов и действий могут взаимодействовать друг с другом, только когда они принадлежат к одному центру задач.

Если несколько приложений-функций совместно используют учетную запись хранения, каждому приложению-функции *необходимо* предоставить отдельное имя центра задач. Учетная запись хранения может содержать несколько центров задач. На следующей схеме показано, что в общей и отдельной учетных записях хранения на одно приложение-функцию приходится один центр задач.

![Схема с общей и отдельной учетными записями хранения.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Ресурсы хранилища Azure

Центр задач состоит из следующих ресурсов хранилища:

* Одна или несколько очередей элементов управления.
* Одна очередь рабочих элементов.
* Одна таблица журнала.
* одна таблица экземпляров.
* Один контейнер хранилища, содержащий один или несколько арендуемых больших двоичных объектов.
* A storage container containing large message payloads, if applicable.

All of these resources are created automatically in the default Azure Storage account when orchestrator, entity, or activity functions run or are scheduled to run. В статье [Производительность и масштабирование в устойчивых функциях (Функции Azure)](durable-functions-perf-and-scale.md) объясняется, как используются эти ресурсы.

## <a name="task-hub-names"></a>Имена центров задач

Центры задач определяются по имени, объявленном в файле *host.json*, как показано в следующем примере:

### <a name="hostjson-functions-20"></a>host.json (Functions 2.0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

### <a name="hostjson-functions-1x"></a>host.json (Функции, версия 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Task hubs can also be configured using app settings, as shown in the following `host.json` example file:

### <a name="hostjson-functions-10"></a>host.json (Functions 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (Functions 2.0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

Для имени центра задач будет установлено значение параметра приложения `MyTaskHub`. В следующем файле `local.settings.json` показано, как определить параметр `MyTaskHub` как `samplehubname`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

The following code is a precompiled C# example of how to write a function that uses the [orchestration client binding](durable-functions-bindings.md#orchestration-client) to work with a task hub that is configured as an App Setting:

### <a name="c"></a>C#

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> The previous C# example is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript"></a>JavaScript

Свойство центра задач в файле `function.json` задается с помощью параметра приложения:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

Имена центра задач должны начинаться с буквы и содержать только буквы и цифры. If not specified, a default task hub name will be used as shown in the following table:

| Durable extension version | Default task hub name |
| - | - |
| 2.x | When deployed in Azure, the task hub name is derived from the name of the _function app_. When running outside of Azure, the default task hub name is `TestHubName`. |
| 1.x | The default task hub name for all environments is `DurableFunctionsHub`. |

For more information about the differences between extension versions, see the [Durable Functions versions](durable-functions-versions.md) article.

> [!NOTE]
> Имя — это то, что отличает один центр задач от другого, если в общей учетной записи хранения таких центров несколько. Если общую учетную запись хранения используют несколько приложений-функций, вам нужно задать разные имена для каждого центра задач в файлах *host.json*. Otherwise the multiple function apps will compete with each other for messages, which could result in undefined behavior, including orchestrations getting unexpectedly "stuck" in the `Pending` or `Running` state.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Learn how to handle orchestration versioning](durable-functions-versioning.md)
