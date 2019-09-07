---
title: Шаблоны Устойчивые функции и технические понятия в функциях Azure
description: Узнайте, как расширение Устойчивые функции в функциях Azure предоставляет преимущества выполнения кода с отслеживанием состояния в облаке.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: fe3000181ed02e3640e7af48fa492f4a7db55191
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734571"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Шаблоны Устойчивые функции и технические понятия (функции Azure)

Устойчивые функции — это расширение [функций Azure](../functions-overview.md) и веб- [заданий Azure](../../app-service/web-sites-create-web-jobs.md). Устойчивые функции можно использовать для записи функций с отслеживанием состояния в бессерверной среде. Расширение автоматически управляет состоянием, создает контрольные точки и перезагружается. 

В этой статье содержатся подробные сведения о поведении расширения Устойчивые функции для функций Azure и распространенных шаблонов реализации. Эти сведения помогут определить, как использовать Устойчивые функции для решения задач разработки.

> [!NOTE]
> Устойчивые функции — это расширенное расширение для функций Azure, которое не подходит для всех приложений. В этой статье предполагается, что вы хорошо знакомы с концепциями в [функциях Azure](../functions-overview.md) и проблемами, связанными с бессерверным развертыванием приложений.

## <a name="patterns"></a>Шаблоны

В этом разделе описываются некоторые распространенные шаблоны приложений, в которых Устойчивые функции может быть полезным.

### <a name="chaining"></a>Шаблон 1. Цепочка функций

В шаблоне цепочки функций последовательность функций выполняется в определенном порядке. В этом шаблоне выходные данные одной функции применяются к входу другой функции.

![Схема шаблона цепочки функций](./media/durable-functions-concepts/function-chaining.png)

Устойчивые функции можно использовать для краткой реализации шаблона цепочки функций, как показано в следующем примере:

#### <a name="precompiled-c"></a>Предкомпилированный код C#

```csharp
public static async Task<object> Run([OrchestrationTrigger] DurableOrchestrationContext context)
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
> Между написанием предварительно скомпилированной устойчивой функции в C# и написанием предварительно скомпилированной устойчивой функции в C# скрипте существуют небольшие различия. C# В предварительно скомпилированной функции надежные параметры должны быть дополнены соответствующими атрибутами. Примером является `[OrchestrationTrigger]` атрибут `DurableOrchestrationContext` для параметра. C# В предварительно скомпилированной устойчивой функции, если параметры не были правильно параметризованы, среда выполнения не может внедрить переменные в функцию, и возникает ошибка. Дополнительные примеры см. в [примерах Azure-functions-устойчивых расширений на сайте GitHub](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

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

`F1`В этом примере значения `F2` `F3`,, и `F4` являются именами других функций в приложении функции. Поток управления можно реализовать с помощью обычных конструкций императивного кода. Код выполняется сверху вниз. Код может включать существующую семантику потока управления языком, например условные выражения и циклы. `try` В блокиможно`finally` включить логику обработки ошибок. / `catch` /

Можно использовать `context` параметр [DurableOrchestrationContext] \(.NET\) и Object(JavaScript)длявызовадругихфункцийпоимени,передачипараметровивыводавозвращаемыхфункций.`context.df` Каждый раз, когда код `await` вызываетC#() `yield` или (JavaScript), устойчивые функции Framework создает контрольный список хода выполнения текущего экземпляра функции. Если процесс или виртуальная машина перезапускается в середине по выполнению, экземпляр функции возобновляется из предыдущего `await` вызова `yield` или. Дополнительные сведения см. в следующем разделе шаблон #2: Вентилятор или вентилятор в.

> [!NOTE]
> Объект в JavaScript представляет весь [контекст функции](../functions-reference-node.md#context-object), а не только параметр [DurableOrchestrationContext] `context`

### <a name="fan-in-out"></a>Шаблон 2. Вентилятор или вентилятор в

В шаблоне "вентилятор исходящих и вентиляторов" вы выполняете несколько функций параллельно, а затем ожидаете завершения всех функций. Часто некоторые статистические операции выполняются с результатами, возвращаемыми функциями.

![Схема схемы развертывания и вентилятора](./media/durable-functions-concepts/fan-out-fan-in.png)

С помощью обычных функций можно выработать Вентилятор, получив в очередь несколько сообщений. Войти обратно — гораздо сложнее. Чтобы выполнить вентилятор в, в обычной функции вы пишете код для трассировки завершения функций, активируемых в очереди, а затем сохраняет выходные данные функции. 

Расширение Устойчивые функции обрабатывает этот шаблон с относительно простым кодом:

#### <a name="precompiled-c"></a>Предкомпилированный код C#

```csharp
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
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

Работа с вентиляторами распространяется на несколько экземпляров `F2` функции. Работа ведется с помощью динамического списка задач. Вызывается `Task.WhenAll` API .NET или `context.df.Task.all` JavaScript API для ожидания завершения всех вызванных функций. Затем выходные `F3` данные функцииобъединяютсяиздинамическогосписказадачипередаются`F2` в функцию.

Автоматическая контрольная точка, которая происходит при `await` вызове `Task.WhenAll` или `yield` , `context.df.Task.all` или гарантирует, что при возникновении потенциального сбоя или перезагрузки не требуется перезапуск уже завершенной задачи.

### <a name="async-http"></a>Шаблон 3. Асинхронные API-интерфейсы HTTP

Шаблон асинхронных API-интерфейсов HTTP решает проблему координирования состояния длительных операций с внешними клиентами. Распространенный способ реализации этого шаблона заключается в том, что вызов HTTP вызывает выполнение длительного действия. Затем перенаправьте клиент на конечную точку состояния, которую Клиент опрашивает для получения сведений о завершении операции.

![Схема шаблона API HTTP](./media/durable-functions-concepts/async-http-api.png)

Устойчивые функции предоставляет встроенные API, упрощающие написание кода для взаимодействия с длительными выполнениями функций. Примеры краткого руководства Устойчивые функции[C#](durable-functions-create-first-csharp.md) (и [JavaScript](quickstart-js-vscode.md)) демонстрируют простую команду, которую можно использовать для запуска новых экземпляров функций Orchestrator. После запуска экземпляра расширение предоставляет интерфейсы API HTTP веб-перехватчика, которые запрашивают состояние функции Orchestrator. 

В следующем примере показаны команды, которые запускают Orchestrator и запрашивают состояние. Для ясности в примере опущены некоторые подробности.

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

Поскольку среда выполнения Устойчивые функции управляет состоянием, вам не нужно реализовывать собственный механизм отслеживания состояния.

Расширение Устойчивые функции имеет встроенные веб-перехватчики, которые управляют долго выполняющимися оркестрации. Этот шаблон можно реализовать самостоятельно с помощью собственных триггеров функций (таких как HTTP, очередь или концентраторы событий Azure) и `orchestrationClient` привязки. Например, сообщение в очереди можно использовать для активации завершения. Также можно использовать триггер HTTP, защищенный Azure Active Directory политикой проверки подлинности вместо встроенных веб-перехватчиков, которые используют созданный ключ для проверки подлинности.

Ниже приведены некоторые примеры использования шаблона API HTTP.

#### <a name="precompiled-c"></a>Предкомпилированный код C#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
[FunctionName("StartNewOrchestration")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
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

#### <a name="c-script"></a>Сценарии C#

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

В среде .NET параметр [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` — это значение из выходных данных `orchestrationClient`, которое является частью расширения Устойчивых функций. В JavaScript этот объект возвращается путем вызова `df.getClient(context)`. Эти объекты предоставляют методы, которые можно использовать для запуска, отправки событий в, завершения и запроса новых или существующих экземпляров функций Orchestrator.

В предыдущих примерах функция, активируемая HTTP, принимает `functionName` значение из входящего URL-адреса и передает значение в [стартневасинк](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Затем API привязки [креатечеккстатусреспонсе](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) возвращает ответ, содержащий `Location` заголовок и дополнительные сведения об экземпляре. Эти сведения можно использовать позже для поиска состояния запущенного экземпляра или для завершения экземпляра.

### <a name="monitoring"></a>Шаблон 4. Монитор

Шаблон монитора относится к гибкому, повторяющемуся процессу в рабочем процессе. В качестве примера выполняется опрос до тех пор, пока не будут выполнены определенные условия. Вы можете использовать регулярный [триггер таймера](../functions-bindings-timer.md) для решения базовой ситуации, например периодического задания очистки, но его интервал является статическим, и управление жизненным циклом экземпляра становится сложным. Устойчивые функции можно использовать для создания гибких интервалов повторения, управления жизненным циклом задач и создания нескольких процессов мониторинга из единого взаимодействия.

Примером шаблона монитора является Отмена предыдущего сценария асинхронного API-интерфейса HTTP. Вместо предоставления конечной точке для внешнего клиента возможности наблюдения за длительной операцией долго выполняющийся монитор использует внешнюю конечную точку, а затем ожидает изменения состояния.

![Схема шаблона монитора](./media/durable-functions-concepts/monitor.png)

В нескольких строках кода можно использовать Устойчивые функции для создания нескольких мониторов, использующих произвольные конечные точки. Мониторы могут завершить выполнение при выполнении условия или [DurableOrchestrationClient](durable-functions-instance-management.md) может завершить работу мониторов. Можно изменить `wait` интервал монитора на основе определенного условия (например, экспоненциальной задержки). 

Следующий код реализует базовый монитор:

#### <a name="precompiled-c"></a>Предкомпилированный код C#

```csharp
[FunctionName("Orchestrator")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
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

При получении запроса создается экземпляр оркестрации для указанного идентификатора события. Экземпляр выполняет опрос состояния, пока не выполнится условие или не произойдет выход из цикла. Устойчивый таймер управляет интервалом опроса. Затем можно выполнить больше работы или завершить согласование. Если (.NET) или `context.df.currentUtcDateTime` `expiryTime` (JavaScript) превышает значение, монитор завершается. `context.CurrentUtcDateTime`

### <a name="human"></a>Шаблон 5. Участие пользователя

Многие автоматизированные процессы подразумевают некоторый тип взаимодействия с человеком. Участие человека в автоматизированном процессе — это непросто, так как люди не являются высокодоступными и отвечают как облачные службы. Автоматический процесс может разрешить это, используя время ожидания и логику компенсации.

Процесс утверждения — это пример бизнес-процесса, включающего участие человека. Утверждение от руководителя может потребоваться для отчета о расходах, превышающих определенный объем в долларах. Если менеджер не утверждает отчет о расходах в течение 72 часов (возможно, менеджер перешел в отпуск), процесс эскалации начинается с того, чтобы получить утверждение от кого-то другого (возможно, руководителя руководителя).

![Схема шаблона взаимодействия с человеком](./media/durable-functions-concepts/approval.png)

В этом примере шаблон можно реализовать с помощью функции Orchestrator. Для запроса утверждения Orchestrator использует [устойчивый таймер](durable-functions-timers.md) . Если истекло время ожидания, Orchestrator. Orchestrator ожидает [внешнее событие](durable-functions-external-events.md), например уведомление, созданное человеком-взаимодействием.

В этих примерах создается процесс утверждения для демонстрации шаблона взаимодействия человека:

#### <a name="precompiled-c"></a>Предкомпилированный код C#

```csharp
[FunctionName("Orchestrator")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
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

Чтобы создать устойчивый таймер, вызовите `context.CreateTimer` (.NET) или `context.df.createTimer` (JavaScript). Уведомление поступает событию `context.WaitForExternalEvent` (.NET) или `context.df.waitForExternalEvent` (JavaScript). Затем вызывается метод `context.df.Task.any` (.NET)или(JavaScript),чтобырешить,следуетлиукрупнение(времяожиданияистекает)илиобработатьутверждение(утверждениепринятодоистечениявремениожидания).`Task.WhenAny`

Внешний клиент может доставить уведомление о событии в ожидающую функцию Orchestrator с помощью [встроенных API HTTP](durable-functions-http-api.md#raise-event) или с помощью API [DurableOrchestrationClient. RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) из другой функции:

#### <a name="precompiled-c"></a>Предкомпилированный код C#

```csharp
public static async Task Run(
  [HttpTrigger] string instanceId,
  [OrchestrationClient] DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

#### <a name="c-script"></a>Скрипт C#

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

#### <a name="javascript"></a>JavaScript

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

### <a name="aggregator"></a>#6 шаблона: Агрегатор (Предварительная версия)

Шестой шаблон заключается в статистической обработке данных событий за определенный период времени в одну доступную для адресации *сущность*. В этом шаблоне данные, для которых выполняется агрегирование, могут поступать из нескольких источников, могут быть доставлены пакетами или разбросаны по длительным периодам времени. Агрегатору может потребоваться предпринять действия над данными событий по мере их поступления, а внешним клиентам может потребоваться запросить агрегированные данные.

![Схема агрегатора](./media/durable-functions-concepts/aggregator.png)

Непростость при реализации этого шаблона с обычными функциями без отслеживания состояния заключается в том, что управление параллелизмом становится огромным задачей. Не только нужно беспокоиться о том, что несколько потоков одновременно изменяют одни и те же данные. также необходимо беспокоиться о том, чтобы агрегатор выполнялся только на одной виртуальной машине за один раз.

Используя [устойчивую функцию сущности](durable-functions-preview.md#entity-functions), можно легко реализовать этот шаблон как единую функцию.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

Устойчивые сущности также можно моделировать как классы .NET. Это может быть полезно, если список операций становится большим и в основном является статическим. Следующий пример представляет собой эквивалентную реализацию `Counter` сущности с помощью классов и методов .NET.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Клиенты могут помещать в очередь *операции* (называемые также "сигнальным") функцией сущности, `orchestrationClient` использующей привязку.

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

Динамически создаваемые прокси-серверы также доступны для сигнализации сущностей строго типизированным способом. Кроме сигнализации, клиенты также могут запрашивать состояние функции сущности с помощью методов `orchestrationClient` привязки.

> [!NOTE]
> Функции сущностей в настоящее время доступны только в [предварительной версии Устойчивые функции 2,0](durable-functions-preview.md).

## <a name="the-technology"></a>Технология

В фоновом режиме расширение Устойчивые функции построено на основе [платформы устойчивых задач](https://github.com/Azure/durabletask), библиотеки с открытым исходным кодом на GitHub, которая используется для создания устойчивых управляемых задач. Подобно функциям Azure, это несерверное развитие веб-заданий Azure, Устойчивые функции — это несерверное развитие платформы устойчивых задач. Корпорация Майкрософт и другие организации широко используют платформу устойчивых задач для автоматизации критически важных процессов. Это естественный выбор для бессерверной среды функций Azure.

### <a name="event-sourcing-checkpointing-and-replay"></a>Источники событий, создание контрольных точек и повторное воспроизведение

Функции Orchestrator надежно сохраняют свое состояние выполнения с помощью шаблона проектирования [источников событий](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) . Вместо непосредственного хранения текущего состояния оркестрации расширение Устойчивые функции использует хранилище, доступное только для добавления, для записи полного набора действий, выполняемых оркестрации функции. Хранилище, доступное только для добавления, имеет много преимуществ по сравнению с "дампом" полного состояния среды выполнения. Преимущества включают повышенную производительность, масштабируемость и скорость реагирования. Вы также получаете окончательную согласованность данных о транзакциях и полный журнал аудита и журналы. Журналы аудита поддерживают надежные компенсирующие действия.

Устойчивые функции использует источники событий прозрачно. В фоновом режиме операторC# `yield` () или (JavaScript) в функции Orchestrator передает управление потоком Orchestrator в Диспетчер платформы устойчивых задач. `await` Затем диспетчер совершает любые новые действия, запланированные функцией оркестратора (например, вызов одной или нескольких дочерних функций или планирование устойчивого таймера) в хранилище. Действие прозрачной фиксации добавляется в журнал выполнения экземпляра оркестрации. Журнал хранится в таблице хранилища. Затем действие фиксации добавляет сообщения в очередь для планирования фактической работы. На этом этапе функцию оркестрации можно выгрузить из памяти. 

Выставление счетов для функции Orchestrator останавливается, если вы используете план потребления функций Azure. При выполнении дополнительных действий функция перезапускается и ее состояние перестраивается.

Если функции оркестрации передается больше работы (например, получается ответное сообщение или истекает устойчивый таймер), Orchestrator выходит из системы и повторно выполняет всю функцию из начала для перестроения локального состояния. 

Во время воспроизведения, если код пытается вызвать функцию (или другие асинхронные операции), платформа устойчивых задач обращается к журналу выполнения текущего оркестрации. Если она обнаружит, что [функция действия](durable-functions-types-features-overview.md#activity-functions) уже выполнена и вернула результат, она воспроизведет результат этой функции, и код Orchestrator продолжит работать. Воспроизведение продолжится до тех пор, пока не завершится выполнение кода функции или пока не будет запланирована новая Асинхронная работа.

### <a name="orchestrator-code-constraints"></a>Ограничения кода оркестратора

Поведение воспроизведения кода Orchestrator создает ограничения на тип кода, который можно написать в функции Orchestrator. Например, код Orchestrator должен быть детерминированным, так как он будет воспроизводиться несколько раз и каждый раз должен выдавать один и тот же результат. Полный список ограничений см. в статье [ограничения кода Orchestrator](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Мониторинг и диагностика

Расширение Устойчивые функции автоматически создает структурированные данные отслеживания, чтобы [Application Insights](../functions-monitoring.md) , если вы настроили приложение функции с помощью ключа инструментирования Azure Application Insights. Данные отслеживания можно использовать для наблюдения за действиями и ходом выполнения согласований.

Ниже приведен пример того, как будут выглядеть события отслеживания Устойчивые функции на портале Application Insights при использовании [аналитики Application Insights](../../application-insights/app-insights-analytics.md):

![Application Insights результатов запроса](./media/durable-functions-concepts/app-insights-1.png)

Полезные структурированные данные можно найти в `customDimensions` поле в каждой записи журнала. Ниже приведен пример полностью развернутой записи:

![Поле customDimensions в запросе Application Insights](./media/durable-functions-concepts/app-insights-2.png)

Из-за поведения воспроизведения диспетчера платформы устойчивых задач возможна избыточность записей журнала повторных действий. Избыточные записи журнала могут помочь понять поведение воспроизведения ядра процессора. В статье о [диагностике](durable-functions-diagnostics.md) показаны образцы запросов, которые отфильтрают журналы воспроизведения, что позволяет просматривать только журналы реального времени.

## <a name="storage-and-scalability"></a>Хранение и масштабируемость

Расширение Устойчивые функции использует очереди, таблицы и большие двоичные объекты в службе хранилища Azure для сохранения состояния журнала выполнения и запуска выполнения функции. Вы можете использовать учетную запись хранения по умолчанию для приложения-функции или настроить отдельную учетную запись хранения. Может потребоваться отдельная учетная запись, основанная на ограничениях пропускной способности хранилища. Код Orchestrator, который вы пишете, не взаимодействует с сущностями в этих учетных записях хранения. Платформа устойчивых задач управляет сущностями непосредственно в виде сведений о реализации.

Функции оркестратора планируют функции действий и получают ответы через сообщения во внутренней очереди. При запуске приложения-функции в плане потребления функций Azure [контроллеры масштабирования функций Azure](../functions-scale.md#how-the-consumption-and-premium-plans-work) отслеживают эти очереди. При необходимости добавляются новые экземпляры вычислений. При масштабировании на несколько виртуальных машин функция Orchestrator может выполняться на одной виртуальной машине, а функции действий, которые вызывает функция Orchestrator, могут выполняться на нескольких разных виртуальных машинах. Дополнительные сведения о поведении масштабирования Устойчивые функции см. в разделе [производительность и масштабирование](durable-functions-perf-and-scale.md).

Журнал выполнения учетных записей Orchestrator хранится в хранилище таблиц. Всякий раз, когда экземпляр восстанавливается на определенной виртуальной машине, Orchestrator извлекает его журнал выполнения из хранилища таблиц, чтобы он мог перестроить свое локальное состояние. Удобный аспект использования журнала в хранилище таблиц заключается в том, что вы можете использовать такие средства, как [Обозреватель службы хранилища Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) , чтобы просмотреть историю согласований.

BLOB-объекты хранилища в основном используются в качестве механизма аренды для координации масштабирования экземпляров оркестрации на нескольких виртуальных машинах. Большие двоичные объекты хранилища хранят данные для больших сообщений, которые не могут храниться непосредственно в таблицах или очередях.

![Снимок экрана Обозреватель службы хранилища Azure](./media/durable-functions-concepts/storage-explorer.png)

> [!NOTE]
> Хотя журнал выполнения в хранилище таблиц легко и удобен для просмотра, не следует создавать зависимости от этой таблицы. Таблица может измениться по мере развития расширения Устойчивые функции.

## <a name="known-issues"></a>Известные проблемы

Следует отслеживать все известные проблемы в списке [проблем на GitHub](https://github.com/Azure/azure-functions-durable-extension/issues). Если возникла проблема и не удается найти проблему в GitHub, откройте новую проблему. Включите подробное описание проблемы.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Устойчивые функции см. в статье [типы и функции устойчивые функции функций](durable-functions-types-features-overview.md). 

Чтобы начать работу:

> [!div class="nextstepaction"]
> [создайте свою первую устойчивую функцию](durable-functions-create-first-csharp.md).

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html