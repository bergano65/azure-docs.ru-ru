---
title: Устойчивые функции шаблонов и технические концепции в функциях Azure
description: Узнайте, как расширение устойчивых функций в функциях Azure предоставляет возможности выполнения кода с отслеживанием состояния в облаке.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: 95ec6a863f951a8c26abd865041c68df333a4e38
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65071344"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Устойчивые функции шаблонов и технические концепции (функции Azure)

Устойчивые функции — это расширение ["функции Azure"](../functions-overview.md) и [веб-заданий Azure](../../app-service/web-sites-create-web-jobs.md). Устойчивые функции можно использовать для написания функций с отслеживанием состояния в бессерверной среде. Расширение автоматически управляет состоянием, создает контрольные точки и перезагружается. 

Эта статья представляет подробные сведения о поведении расширения устойчивых функций для функций Azure и распространенные шаблоны реализации. Сведения могут помочь определить, как использовать устойчивые функции для решения задач разработки.

> [!NOTE]
> Устойчивые функции — это дополнительное расширение функций Azure, не подходит для всех приложений. В этой статье предполагается, что у вас есть хорошо знакомы с основными понятиями в ["функции Azure"](../functions-overview.md) и сложностей, участвующих в разработке бессерверных приложений.

## <a name="patterns"></a>Шаблоны

В этом разделе описаны некоторые распространенные шаблоны приложений, которых можно использовать устойчивые функции.

### <a name="chaining"></a>Шаблон 1. Цепочка функций

В функции цепочки шаблон последовательности функций выполняет в определенном порядке. В этом случае выходные данные одной функции применяется в качестве входных данных другой функции.

![Схема создания цепочки шаблон функций](./media/durable-functions-concepts/function-chaining.png)

Устойчивые функции можно использовать для выполнения функции цепочки шаблон кратко в том случае, как показано в следующем примере:

#### <a name="c-script"></a>Сценарии C#

```csharp
public static async Task<object> Run(DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

> [!NOTE]
> Существуют небольшие отличия между написанием предкомпилированного устойчивые функции C# и написания предкомпилированного устойчивые функции C# скрипт, который показан в примере. В C# предкомпилированная функция, устойчивые параметров должен быть снабжен атрибутом соответствующие атрибуты. Например, `[OrchestrationTrigger]` для атрибута `DurableOrchestrationContext` параметра. В C# предкомпилированных устойчивой функции, если параметры не снабжен должным образом, среда выполнения невозможно ввести переменные в функцию и возникает ошибка. Дополнительные примеры см. в разделе [azure-functions-durable-extension примеры на GitHub](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

В этом примере значения `F1`, `F2`, `F3`, и `F4` являются именами других функций в приложении-функции. Поток управления можно реализовать с помощью обычных принудительных конструкций программирования. Код выполняется сверху вниз. Код может включать существующие семантику языка потока управления, такие как условные выражения и циклы. Можно включить логику обработки ошибок `try` / `catch` / `finally` блоков.

Можно использовать `context` параметр [DurableOrchestrationContext] \(.NET\) и `context.df` объект (JavaScript) для вызова других функций по имени, передачи параметров и возвращения функции выходные данные. Каждый раз, когда код вызывает метод `await` (C#) или `yield` (JavaScript), платформа устойчивых функций контрольные точки ход выполнения текущего экземпляра функции. Если процесс или виртуальная машина перезапускается во время выполнения, экземпляр функции возобновляется из предыдущей `await` или `yield` вызова. Дополнительные сведения см. в разделе разделу, шаблон № 2: Вентилятор out/входу.

> [!NOTE]
> `context` В JavaScript представляет весь [контексте функции](../functions-reference-node.md#context-object), не только [DurableOrchestrationContext] параметра.

### <a name="fan-in-out"></a>Шаблон 2. Вентилятор out/вентилятор в

В вентилятора out/вентилятор в шаблоне, можно параллельно выполнять несколько функций, а затем подождите, для выполнения всех функций. Часто некоторые агрегирования завершаются по результатам, которые возвращаются из функций.

![Схема вентилятора out/вентилятор шаблон](./media/durable-functions-concepts/fan-out-fan-in.png)

При использовании обычных функций вы можете развернуть за счет отправки функцией нескольких сообщений в очередь. Войти обратно является намного более сложной задачей. Для вентиляторов, в обычную функцию, необходимо написать код для отслеживания, когда активируемые очередью функции end, а затем магазина функция выходные данные. 

Расширение устойчивых функций обрабатывает этот шаблон с относительно простого кода:

#### <a name="c-script"></a>Сценарии C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Процесс развертывания распределяется на несколько экземпляров `F2` функции. Отслеживается с помощью динамического списка задач. .NET `Task.WhenAll` API или JavaScript `context.df.Task.all` вызывается API, чтобы дождаться завершения всех вызванных функций. Затем `F2` функция выводит собираются из списка динамических задач и передается `F3` функции.

Автоматическая запись контрольных точек во `await` или `yield` вызвать `Task.WhenAll` или `context.df.Task.all` гарантирует, что потенциальные находится ровно посредине сбоя или перезагрузка не требует перезапуска уже выполненной задачи.

### <a name="async-http"></a>Шаблон 3. Асинхронные API-интерфейсы HTTP

Асинхронный шаблон API-интерфейсы HTTP устраняет проблемы, связанные сложностей координации состояния долговременных операций с внешними клиентами. За счет вызова триггера долго выполняющееся действие HTTP — распространенный способ реализации этого шаблона. Затем перенаправлять клиента в конечную точку состояния, который клиент отправляет дополнительные завершения операции.

![Схема шаблона HTTP API](./media/durable-functions-concepts/async-http-api.png)

Устойчивые функции предоставляют встроенные API, которые упрощают код, создаваемый для взаимодействия с выполняющейся длительное время выполнения функций. Примеры для быстрого запуска устойчивых функций ([ C# ](durable-functions-create-first-csharp.md) и [JavaScript](quickstart-js-vscode.md)) Показать простая команда REST, который можно использовать для запуска новых экземпляров функции оркестратора. После запуска экземпляра расширение предоставляет веб-перехватчика HTTP API, отправляющие запросы состояние функции оркестратора. 

В следующем примере показано REST команд запуска оркестратора и запроса его состояния. Для ясности в примере опущены некоторые подробности.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Так как среда выполнения устойчивых функций управляет состоянием, не нужно реализовать собственный механизм отслеживания состояния.

Расширение устойчивых функций имеет встроенные веб-перехватчики, управление длительными взаимодействиями. Вы можете реализовать этот шаблон самостоятельно, используя свои собственные триггеры функций (например, HTTP, очередь или концентраторов событий) и `orchestrationClient` привязки. Например можно использовать сообщение очереди для активации действия прекращения. Кроме того, может использовать триггер HTTP, который защищается политику проверки подлинности Azure Active Directory вместо встроенных веб-перехватчиков созданный ключ для проверки подлинности.

Ниже приведены некоторые примеры того, как использовать шаблон HTTP API.

#### <a name="c"></a>C#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
// An HTTP-triggered function starts a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // The function name comes from the request URL.
    // The function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

В среде .NET параметр [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` — это значение из выходных данных `orchestrationClient`, которое является частью расширения Устойчивых функций. В JavaScript этот объект возвращается путем вызова `df.getClient(context)`. Эти объекты предоставляют методы, которые можно использовать для запуска, отправки событий, завершения и запросов для экземпляров функции оркестратора новую или существующую.

В предыдущих примерах, принимает функцию, активируемую HTTP `functionName` значения из входящего URL-адреса и передает значение [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) затем привязки API возвращает ответ, содержащий `Location` заголовок и Дополнительные сведения об экземпляре. Данные можно использовать позже для поиска состояния запущенного экземпляра или завершить его работу.

### <a name="monitoring"></a>Шаблон 4. Мониторинг

Шаблон монитора представляет процесс гибкая, повторяющихся в рабочем процессе. Примером опрос, пока не будут выполнены определенные условия. Можно использовать обычный [триггера таймера](../functions-bindings-timer.md) для реализации простой сценарий, например задание периодической очистки, но его интервал является статическим, и усложняет управление временем существования экземпляра. Устойчивые функции позволяют создавать гибкие интервалы повторения, управление временем существования задачи и создавать несколько процессов мониторинга в рамках одной оркестрации.

Пример шаблона монитора является в обратном порядке более ранних сценарий асинхронных HTTP API. Вместо предоставления конечной точки внешнему клиенту для отслеживания длительной операции, выполняющейся длительное время монитор использует внешнюю конечную точку и затем ожидать изменения состояния.

![Схема шаблона монитора](./media/durable-functions-concepts/monitor.png)

Несколько строк кода можно использовать устойчивые функции для создания нескольких мониторов, которые наблюдают за произвольными конечными точками. Мониторов может приостановиться, если условие выполняется, или [DurableOrchestrationClient](durable-functions-instance-management.md) может завершить мониторов. Вы можете изменить монитор `wait` интервал на основе определенного условия (например, экспоненциальную задержку.) 

Следующий код реализуется простой монитор:

#### <a name="c-script"></a>Сценарии C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

При получении запроса создается экземпляр оркестрации для указанного идентификатора события. Экземпляр выполняет опрос состояния, пока не выполнится условие или не произойдет выход из цикла. Устойчивый таймер управляет интервалом опроса. Затем можно выполнить дополнительную работу, или оркестрации можно завершить. Когда `context.CurrentUtcDateTime` (.NET) или `context.df.currentUtcDateTime` (JavaScript) превышает `expiryTime` значение, монитор останавливается.

### <a name="human"></a>Шаблон 5. Участие пользователя

Многие автоматизированные процессы требуют некоторого участия пользователя. Связанная с вовлечением людей в автоматизированный процесс достаточно сложно реализовать, что людям не как высокодоступная и себя, как облачные службы. Автоматизированный процесс может разрешить для этого с помощью времени ожидания и логика компенсации.

Процесс утверждения-это пример бизнес-процесса, который включает в себя участие. Утверждение от менеджера может потребоваться для отчета о расходах, превышающих определенную сумму доллара. Если менеджер не утверждает отчет по расходам в течение 72 часов (возможно диспетчер перешел в отпуске), начинается процесс эскалации, чтобы получить одобрение от кого-нибудь (возможно, руководителя этого руководителя).

![Схема шаблона действий человека](./media/durable-functions-concepts/approval.png)

Можно реализовать шаблон в этом примере с помощью функции оркестратора. Оркестратор использует [устойчивого таймера](durable-functions-timers.md) в утверждение запроса. Оркестратор перехода в случае превышения времени ожидания. Оркестратор ожидает [внешнее событие](durable-functions-external-events.md), такие как уведомление, которое формируется путем участия пользователя.

В этих примерах создается процесс утверждения для демонстрации шаблона действий человека:

#### <a name="c-script"></a>Сценарии C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Чтобы создать устойчивый таймер, вызовите `context.CreateTimer` (.NET) или `context.df.createTimer` (JavaScript). Уведомление поступает событию `context.WaitForExternalEvent` (.NET) или `context.df.waitForExternalEvent` (JavaScript). Затем `Task.WhenAny` (.NET) или `context.df.Task.any` (JavaScript) вызывается, чтобы решить, следует ли расширять (сначала время ожидания истечет) или обработать утверждения (утверждение получено до истечения времени ожидания).

Внешний клиент может предоставлять уведомления о событии ожидающая функция оркестратора либо при помощи [встроенных API-интерфейсов HTTP](durable-functions-http-api.md#raise-event) или с помощью [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) API из другая функция:

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

## <a name="pattern-6-aggregator-preview"></a>Шаблон #6: Средство инвентаризации программного обеспечения (Предварительная версия)

Шестой шаблон посвящен статистическая обработка данных событий за период времени, в единую адресуемый *сущности*. В этом шаблоне, подвергаемый статистической обработке данных могут поступать из нескольких источников могут доставляться в пакетах и могут быть разбросаны за длинные интервалы времени. Средство инвентаризации программного обеспечения может потребоваться выполнить действие на данные события, как их поступления, и внешним клиентам может потребоваться направить запрос объединенные данные.

![Схема средства инвентаризации программного обеспечения](./media/durable-functions-concepts/aggregator.png)

То непростой задачей, что требуется реализовать этот шаблон при обычной, функций без отслеживания состояния является управление параллелизмом вызывают большие трудности. Не только нужно беспокоиться о нескольких потоков, изменяющих эти же данные в то же время, необходимо также беспокоиться об обеспечении, что средство инвентаризации программного обеспечения выполняется только на одной виртуальной Машине за раз.

С помощью [функция устойчивых сущности](durable-functions-preview.md#entity-functions), можно реализовать этот шаблон легко в виде одной функции.

```csharp
public static async Task Counter(
    [EntityTrigger(EntityClassName = "Counter")] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    int operand = ctx.GetInput<int>();

    switch (ctx.OperationName)
    {
        case "add":
            currentValue += operand;
            break;
        case "subtract":
            currentValue -= operand;
            break;
        case "reset":
            await SendResetNotificationAsync();
            currentValue = 0;
            break;
    }

    ctx.SetState(currentValue);
}
```

Клиенты можно поставить в очередь *операций* за (также называется «оповещение») функция сущности с помощью `orchestrationClient` привязки.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [OrchestrationClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

Аналогичным образом, клиенты могут запрашивать состояние функции сущности с помощью методов `orchestrationClient` привязки.

> [!NOTE]
> Функции сущности сейчас доступны только в [устойчивых функций 2.0 preview](durable-functions-preview.md).

## <a name="the-technology"></a>Технология

На самом деле расширение устойчивых функций строится на базе [платформа устойчивых задач](https://github.com/Azure/durabletask), библиотеку открытым исходным кодом на GitHub, который используется для создания оркестраций устойчивых задач. Как и функции Azure являются бессерверным развитием веб-заданий Azure, устойчивые функции — это бессерверное развитие платформы устойчивых задач. Корпорация Майкрософт и другими организациями широко использовать платформа устойчивых задач для автоматизации критически важных процессов. Это естественный выбор для бессерверной среды функций Azure.

### <a name="event-sourcing-checkpointing-and-replay"></a>Источники событий, создание контрольных точек и повторное воспроизведение

Функции оркестратора надежно сохраняют свое состояние выполнения с помощью [источников событий](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) шаблон разработки. Вместо непосредственного сохранения текущего состояния оркестрации, расширение устойчивых функций использует инкрементируемое хранилище для записи полных серий действий, которые принимает из функции оркестрации. Инкрементируемое хранилище имеет множество преимуществ по сравнению с «дамп» полного состояния среды выполнения. Преимущества повышения производительности, масштабируемости и скорости реагирования. Можно также получить итоговой согласованности для транзакционных данных и всех аудиторских следов и журнала. Аудиторские следы поддерживает надежные компенсирующие действия.

Устойчивых функций использует прозрачно источников событий. На самом деле `await` (C#) или `yield` оператор (JavaScript) в функцию оркестратора передает управление потоком оркестратора обратно диспетчеру платформы устойчивых задач. Затем диспетчер совершает любые новые действия, запланированные функцией оркестратора (например, вызов одной или нескольких дочерних функций или планирование устойчивого таймера) в хранилище. Прозрачное действие фиксации добавляется в журнал выполнения экземпляра оркестрации. Журнал хранится в таблице хранилища. Затем действие фиксации добавляет сообщения в очередь для планирования фактической работы. На этом этапе функцию оркестрации можно выгрузить из памяти. 

Выставление счетов для функции оркестратора останавливается, если вы используете план потребления функций Azure. Когда имеется больше работы, перезагрузки функции, и ее состояние восстанавливается.

Если функции оркестрации получают больше работы (например, Получено ответное сообщение, или истечения срока действия устойчивого таймера), оркестратор активируется и повторно запускает всю функцию с самого начала, чтобы перестроить локальное состояние. 

Во время воспроизведения, если код пытается вызвать функцию (или выполнить любую другую асинхронную работы), платформа устойчивых задач просматривает журнал выполнения текущей оркестрации. Если он обнаруживает, что [функция действия](durable-functions-types-features-overview.md#activity-functions) уже выполнена и дали результат, он воспроизводит результат этой функции и код оркестратора продолжает выполняться. Воспроизведение продолжается до завершения кода самой функции или по расписанию оно должно новой асинхронной работы.

### <a name="orchestrator-code-constraints"></a>Ограничения кода оркестратора

Поведение воспроизведения оркестратора кода создает ограничивает тип кода, который может записывать в функцию оркестратора. Например код оркестратора должен быть детерминированным, так как оно вызов будет воспроизведен несколько раз, и он должен создавать тот же результат при каждом. Полный список ограничений, см. в разделе [ограничения кода оркестратора](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Мониторинг и диагностика.

Расширение устойчивых функций автоматически отправляет структурированные данные отслеживания для [Application Insights](../functions-monitoring.md) Если вы настроили приложение-функцию с помощью ключа инструментирования Azure Application Insights. Данные отслеживания можно использовать для отслеживания хода выполнения ваших оркестраций и действий.

Ниже приведен пример вида события отслеживания устойчивых функций на портале Application Insights при использовании [аналитики Application Insights](../../application-insights/app-insights-analytics.md):

![Результаты запроса анализа приложения](./media/durable-functions-concepts/app-insights-1.png)

Можно найти полезные данные, структурированные в `customDimensions` в каждой записи журнала. Ниже приведен пример элемента, полностью развернут:

![Поле customDimensions в запросе Application Insights](./media/durable-functions-concepts/app-insights-2.png)

Из-за поведения воспроизведения диспетчера платформы устойчивых задач возможна избыточность записей журнала повторных действий. Избыточность записей журнала, помогут вам понять поведение платформы обработки. [Диагностики](durable-functions-diagnostics.md) статье показаны примеры запросов, которые отфильтровывают журналы воспроизведения, чтобы можно было видеть только журналы «в режиме реального времени».

## <a name="storage-and-scalability"></a>Хранение и масштабируемость

Расширение устойчивых функций использует очереди, таблицы и большие двоичные объекты в службе хранилища Azure для сохранения выполнения журнал состояния и активировать выполнение функции. Можно использовать учетную запись хранения по умолчанию для приложения-функции, или можно настроить отдельную учетную запись хранения. Может потребоваться отдельная учетная запись на основе ограничений пропускной способности хранилища. Создаваемый код оркестратора не взаимодействует с сущностями в эти учетные записи хранения. Платформа устойчивых задач управляет сущностей непосредственно от реализации.

Функции оркестратора планируют функции действий и получают ответы через сообщения во внутренней очереди. При выполнении приложения в плане потребления функций Azure, [контроллер масштабирования функций Azure](../functions-scale.md#how-the-consumption-and-premium-plans-work) отслеживает эти очереди. При необходимости, добавляются новые экземпляры вычислений. При масштабировании на нескольких виртуальных машин, функции оркестратора могут выполняться на одной виртуальной Машине, во время функции действий, которые могут выполняться вызовы функций оркестратора на нескольких виртуальных машинах. Дополнительные сведения о поведении масштабирования устойчивых функций, см. в разделе [производительность и масштабируемость](durable-functions-perf-and-scale.md).

Журнал выполнения для учетных записей оркестратора хранится в хранилище таблиц. Каждый раз, когда экземпляр восстанавливается на определенной виртуальной Машины, оркестратор извлекает журнал выполнения из хранилища таблиц, поэтому его можно восстановить свое локальное состояние. Удобный аспектом наличия журнала в хранилище таблиц — что можно использовать такие средства, как [обозреватель службы хранилища Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) Чтобы просмотреть историю ваших оркестраций.

Хранилище BLOB-объектов в основном используются как механизм аренды для координации горизонтального масштабирования экземпляров оркестрации между несколькими виртуальными машинами. Большие двоичные объекты хранилища хранят данные для больших сообщений, которые не могут храниться напрямую в таблицы или очереди.

![Снимок экрана обозревателя службы хранилища Azure](./media/durable-functions-concepts/storage-explorer.png)

> [!NOTE]
> Несмотря на то, что это легко и удобно просматривать журнал выполнения в хранилище таблиц, не делайте все зависимости для этой таблицы. Таблицы должны измениться по мере развития расширение устойчивых функций.

## <a name="known-issues"></a>Известные проблемы

Следует отслеживать все известные проблемы в списке [проблем на GitHub](https://github.com/Azure/azure-functions-durable-extension/issues). Если вы столкнулись с проблемами и не удается найти проблему в GitHub, откройте новый вопрос. Укажите ее подробное описание проблемы.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об устойчивых функций см. в разделе [устойчивые функции функций типам и возможностям](durable-functions-types-features-overview.md). 

Чтобы начать работу:

> [!div class="nextstepaction"]
> [создайте свою первую устойчивую функцию](durable-functions-create-first-csharp.md).

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
