---
title: Обработка внешних событий в устойчивых функциях в Azure
description: Сведения о том, как обрабатывать внешние события в расширении устойчивых функций для Функций Azure.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 387b5d920de4a295366cc7e948862a12cea901d3
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165555"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Обработка внешних событий в устойчивых функциях (Функции Azure)

Функции оркестратора могут ожидать передачи внешних событий. Эта возможность [устойчивых функций](durable-functions-overview.md) часто используется для обработки действий человека или других внешних триггеров.

> [!NOTE]
> Внешние события это односторонние асинхронные операции. Они не подходят для ситуаций, где клиенту, отправляющему событие, требуется синхронный ответ функции оркестратора.

## <a name="wait-for-events"></a>Ожидание событий

Методы [ваитфорекстерналевент](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) и `waitForExternalEvent` (JavaScript) [привязки триггера оркестрации](durable-functions-bindings.md#orchestration-trigger) позволяют функции Orchestrator асинхронно ждать и ожидать внешнего события. Функция оркестратора, ожидающая передачи данных, объявляет *имя* события и *форму данных*, которую ожидает получить.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> Предыдущий код C# предназначен для Устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext` . Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

---

В предыдущем примере ожидается передача определенного одиночного события и предпринимается действие при его получении.

Можно одновременно прослушивать несколько событий, как показано в следующем примере, в котором ожидается одно из трех возможных уведомлений о событиях.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> Предыдущий код C# предназначен для Устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext` . Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

---

В предыдущем примере ожидается *любое* из нескольких событий. Также возможно реализовать ожидание *всех* событий.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> Предыдущий код предназначен для Устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext` . Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

Если полезные данные события невозможно преобразовать в ожидаемый тип `T`, в .NET выдается исключение.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

---

`WaitForExternalEvent`Ожидание некоторых входных данных в течение неограниченного времени.  Приложение-функцию можно безопасно выгрузить во время ожидания. Когда событие поступит в этот экземпляр оркестрации, он автоматически активируется и немедленно обработает событие.

> [!NOTE]
> Если в приложении-функции используется план потребления, когда функция оркестратора ожидает задачи от `WaitForExternalEvent` (.NET) или`waitForExternalEvent` (JavaScript), плата не взимается, независимо от того, сколько длится ожидание.

## <a name="send-events"></a>Отправка событий

Для отправки внешнего события в оркестрации можно использовать методы [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) или `raiseEventAsync` (JavaScript). Эти методы предоставляются привязкой [клиента оркестрации](durable-functions-bindings.md#orchestration-client) . Чтобы отправить внешнее событие в оркестрации, можно также использовать встроенный [API-интерфейс генерации событий](durable-functions-http-api.md#raise-event) .

Вызванное событие включает в себя *идентификатор экземпляра*, *EventName*и *EVENTDATA* в качестве параметров. Функции Orchestrator обрабатывайте эти события с помощью `WaitForExternalEvent` интерфейсов API (.NET) или `waitForExternalEvent` (JavaScript). Для обработки события значение *EventName* должно совпадать с окончанием отправки и получения. Данные события также должны быть сериализуемыми в формат JSON.

На внутреннем уровне механизм "поднять событие" помещает в очередь сообщение, которое выбирается ожидающей функцией Orchestrator. Если экземпляр не ожидает *события с указанным именем*, сообщение о событии добавляется в очередь в памяти. Если экземпляр оркестрации позже начинает ожидать *события с этим именем*, он проверяет сообщения о событиях в очереди.

> [!NOTE]
> Если экземпляр оркестрации с указанным *идентификатором экземпляра* отсутствует, сообщение о событии отменяется.

Ниже приведен пример функции, активируемой с помощью очереди, которая отправляет событие "Approval" в экземпляр функции оркестратора. Идентификатор экземпляра оркестрации поступает из текста сообщения очереди.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> Предыдущий код C# предназначен для Устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать `OrchestrationClient` атрибут, а не `DurableClient` атрибут, и `DurableOrchestrationClient` тип параметра следует использовать вместо `IDurableOrchestrationClient` . Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

---

Внутри системы `RaiseEventAsync` (.NET) или `raiseEvent`(JavaScript) помещает в очередь сообщение, которое получает ожидающая функция оркестратора. Если экземпляр не ожидает *события с указанным именем*, сообщение о событии добавляется в очередь в памяти. Если экземпляр оркестрации позже начинает ожидать *события с этим именем*, он проверяет сообщения о событиях в очереди.

> [!NOTE]
> Если экземпляр оркестрации с указанным *идентификатором экземпляра* отсутствует, сообщение о событии отменяется.

### <a name="http"></a>HTTP

Ниже приведен пример HTTP-запроса, который вызывает событие "утверждение" в экземпляр оркестрации. 

```http
POST /runtime/webhooks/durabletask/instances/MyInstanceId/raiseEvent/Approval&code=XXX
Content-Type: application/json

"true"
```

В этом случае идентификатор экземпляра жестко закодирован как *минстанцеид*.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Узнайте, как реализовать обработку ошибок](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Запуск примера с ожиданием действий человека](durable-functions-phone-verification.md)