---
title: Центры задач в устойчивых функциях — Azure
description: Сведения о том, что такое центр задач в расширении устойчивых функций для Функций Azure. Узнайте, как настроить концентраторы задач.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 427ab6c4e0e769ab881af0af3023d514c1b092c6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604619"
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
* Контейнер для хранения, содержащий большие полезные нагрузки сообщений, если это применимо.

Все эти ресурсы автоматически создаются в учетной записи хранилища azure по умолчанию при запуске функций оркестратора, сущности или деятельности или запланированы к запуску. В статье [Производительность и масштабирование в устойчивых функциях (Функции Azure)](durable-functions-perf-and-scale.md) объясняется, как используются эти ресурсы.

## <a name="task-hub-names"></a>Имена центров задач

Концентраторы задач идентифицируются по имени, которое соответствует этим правилам:

* Содержит только алфавитные символы
* Начинается с письма
* Имеет минимальную длину 3 символов, максимальную длину 45 символов

Имя концентратора задач объявляется в файле *host.json,* как показано в следующем примере:

### <a name="hostjson-functions-20"></a>host.json (Функции 2.0)

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

Концентраторы задач также могут быть настроены с `host.json` помощью настроек приложения, как показано в следующем примере файла:

### <a name="hostjson-functions-10"></a>host.json (Функции 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (Функции 2.0)

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

Следующий код демонстрирует, как написать функцию, которая использует [связывание клиента оркестровки](durable-functions-bindings.md#orchestration-client) для работы с концентратором задач, настроенным как настройка приложения:

# <a name="c"></a>[C#](#tab/csharp)

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
> Предыдущий пример сC —для прочных функций 2.x. Для долгосрочных функций 1.x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext`. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Свойство центра задач в файле `function.json` задается с помощью параметра приложения:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

Имена центров задач должны начинаться с буквы и содержать только буквы и цифры. Если не указано, имя концентратора задачи по умолчанию будет использоваться, как показано в следующей таблице:

| Прочная версия расширения | Имя концентратора задач по умолчанию |
| - | - |
| 2.x | При развертывании в Azure имя концентратора задач происходит от имени _приложения функции._ При запуске за пределами Azure `TestHubName`имя концентратора задач по умолчанию — это имя . |
| 1.x | Имя концентратора задач `DurableFunctionsHub`по умолчанию для всех сред — это значение. |

Для получения дополнительной информации о различиях между версиями расширения см. [Durable Functions versions](durable-functions-versions.md)

> [!NOTE]
> Имя — это то, что отличает один центр задач от другого, если в общей учетной записи хранения таких центров несколько. Если общую учетную запись хранения используют несколько приложений-функций, вам нужно задать разные имена для каждого центра задач в файлах *host.json*. В противном случае приложения с несколькими функциями будут конкурировать друг с другом за сообщения, `Pending` `Running` что может привести к неопределенному поведению, включая несогласованности, неожиданно "застрявшие" в состоянии или состоянии.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Узнайте, как обрабатывать версии оркестровки](durable-functions-versioning.md)
