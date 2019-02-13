---
title: Центры задач в устойчивых функциях — Azure
description: Сведения о том, что такое центр задач в расширении устойчивых функций для Функций Azure. Сведения о настройке центров задач.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: 596eedab39ff926fcdc880c82c49ac464b7ff23b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753476"
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

Все эти ресурсы создаются автоматически в учетной записи хранилища Azure по умолчанию, когда функции оркестраторов и действий выполняются или запланированы к выполнению. В статье [Производительность и масштабирование в устойчивых функциях (Функции Azure)](durable-functions-perf-and-scale.md) объясняется, как используются эти ресурсы.

## <a name="task-hub-names"></a>Имена центров задач

Центры задач определяются по имени, объявленном в файле *host.json*, как показано в следующем примере:

### <a name="hostjson-functions-1x"></a>host.json (Функции, версия 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json (Функции, версия 2.x)

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

Центры задач также можно настроить с помощью параметров приложения, как показано в следующем примере файла *host.json*:

### <a name="hostjson-functions-1x"></a>host.json (Функции, версия 1.x)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json (Функции, версия 2.x)

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

Ниже приведен предкомпилированный пример C# написания функции, использующей [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) для работы с центром задач, настроенным в качестве параметра приложения:

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] DurableOrchestrationClientBase starter,
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

Ниже приведена необходимая конфигурация для JavaScript. Свойство центра задач в файле `function.json` задается с помощью параметра приложения:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

Имена центров задач должны начинаться с буквы и содержать только буквы и цифры. Если имя не задано, используется имя по умолчанию — **DurableFunctionsHub**.

> [!NOTE]
> Имя — это то, что отличает один центр задач от другого, если в общей учетной записи хранения таких центров несколько. Если общую учетную запись хранения используют несколько приложений-функций, вам нужно задать разные имена для каждого центра задач в файлах *host.json*. В противном случае несколько приложений-функций будут конкурировать друг с другом за сообщения, что может привести к непредсказуемому поведению.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Сведения об обработке управления версиями](durable-functions-versioning.md)
