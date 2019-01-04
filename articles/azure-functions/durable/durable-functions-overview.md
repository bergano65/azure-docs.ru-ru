---
title: Обзор устойчивых функций — Azure
description: Общие сведения о расширении устойчивых функций для Функций Azure.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/7/2018
ms.author: azfuncdf
ms.openlocfilehash: cf643c8c2a0921cfbe1f5599a9e96a27770ef1ef
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718594"
---
# <a name="durable-functions-overview"></a>Обзор устойчивых функций

*Устойчивые функции* являются расширением [Функций Azure](../functions-overview.md) и [веб-заданий Azure](../../app-service/webjobs-create.md), которое позволяет писать функции с отслеживанием состояния в безсерверной среде. Расширение автоматически управляет состоянием, создает контрольные точки и перезагружается.

Расширение позволяет вам определять рабочие процессы с отслеживанием состояния в новом типе функции, называемой [*функцией оркестратора*](durable-functions-types-features-overview.md#orchestrator-functions). Ниже приведены некоторые преимущества функции оркестратора.

* Они определяют рабочие процессы в коде. Не требуются схемы JSON или конструкторы.
* Они могут вызывать другие функции синхронно и асинхронно. Выходные данные вызванной функции могут быть сохранены в локальных переменных.
* Они автоматически создают контрольную точку этапа каждый раз, когда функция ожидает выполнения. Локальное состояние не теряется при перезапуске процесса или при перезагрузке виртуальной машины.

> [!NOTE]
> Устойчивые функции — это дополнительное расширение решения "Функции Azure", которое подходит не для всех приложений. В оставшейся части этой статьи предполагается, что вы хорошо знакомы с основными понятиями [Функций Azure](../functions-overview.md) и сложностями, связанными с разработкой безсерверных приложений.

В основном устойчивые функции используются для упрощения координации с отслеживанием состояния в безсерверных приложениях. В следующих разделах описываются типичные шаблоны приложений, в которых можно эффективно использовать устойчивые функции.

## <a name="pattern-1-function-chaining"></a>Шаблон 1. Цепочка функций

*Цепочкой функций* называют схему выполнения последовательности функций в определенном порядке. Часто требуется передать выходные данные одной функции во входные данные другой.

![Схема цепочки функций](./media/durable-functions-overview/function-chaining.png)

Устойчивые функции позволяют реализовать этот шаблон в коде кратко.

### <a name="c-script"></a>Сценарии C#

```cs
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
        // error handling/compensation goes here
    }
}
```

> [!NOTE]
> При записи предкомпилированная устойчивая функция в C# будет немного отличаться от примера скрипта C#, приведенного ранее. Для предкомпилированной функции в C# потребуется, чтобы параметры устойчивых функций были указаны с соответствующими атрибутами. Например, параметр `DurableOrchestrationContext` нужно указать с атрибутом `[OrchestrationTrigger]`. Если не оформить параметры должным образом, среда выполнения не сможет включить переменные в функции, что приведет к ошибке. Дополнительные примеры см. [здесь](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```js
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

Значения F1, F2, F3 и ​​F4 являются именами других функций в приложении-функции. Поток управления реализуется с помощью обычных принудительных конструкций программирования. То есть код выполняется сверху вниз и может включать в себя существующую семантику языка потока управления, такую ​​как условные обозначения и циклы.  Логика обработки ошибок может быть включена в блоки try/catch/finally.

Параметр `context` ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)) (.NET) и объект `context.df` (JavaScript) предоставляют методы для вызова других функций по имени, передачи параметров и возврата выходных данных функции. Каждый раз, когда код вызывает `await` (C#) или `yield` (JavaScript), платформа Устойчивых функций создает *контрольные точки* выполнения текущего экземпляра функции. Если процесс или виртуальная машина перезапускается во время выполнения, экземпляр функции возобновляется из предыдущего вызова `await` или `yield`. Подробнее об этом поведении перезапуска вы узнаете позже.

> [!NOTE]
> Объект `context` в JavaScript представляет [контекст функции в целом], а не DurableOrchestrationContext.(../functions-reference-node.md#context-object).

## <a name="pattern-2-fan-outfan-in"></a>Шаблон 2. Развертывание/вход

*Развертывание и объединение* относится к шаблону параллельного выполнения нескольких функций, а затем ожидания их завершения.  Часто некоторые агрегирования завершаются по результатам, возвращаемым функциями.

![Схема "Развертывание и объединение"](./media/durable-functions-overview/fan-out-fan-in.png)

При использовании обычных функций развертывание можно выполнить за счет отправки функцией нескольких сообщений в очередь. Тем не менее войти обратно является намного более сложной задачей. Вам нужно будет написать код для отслеживания момента, когда функции, активируемые очередью, завершаются и их выходные значения сохраняются. Расширение устойчивых функций обрабатывает этот шаблон с помощью относительно простого кода.

### <a name="c-script"></a>Сценарии C#

```cs
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // get a list of N work items to process in parallel
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```js
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // get a list of N work items to process in parallel
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // aggregate all N outputs and send result to F3
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Процесс развертывания распределяется по нескольким экземплярам функции `F2` и отслеживается с использованием динамического списка задач. Вызывается API .NET `Task.WhenAll` или API JavaScript `context.df.Task.all` для ожидания завершения всех вызванных функций. Затем выходные данные функции `F2` агрегируются из списка динамических задач и передаются функции `F3`.

Автоматическое создание контрольных точек, которое происходит при вызове `await` или `yield` к `Task.WhenAll` или `context.df.Task.all`, гарантирует, что любой сбой или перезагрузка во время выполнения не потребуют перезапуска уже завершенных задач.

## <a name="pattern-3-async-http-apis"></a>Шаблон 3. Асинхронные API-интерфейсы HTTP

Третий шаблон предназначен для решения сложностей координации состояния долговременных операций с внешними клиентами. Общим способом реализации этого шаблона является долго выполняющееся действие, активированное вызовом HTTP, а затем перенаправление клиента в конечную точку состояния, которую он может опросить, чтобы узнать о завершении операции.

![Схема API HTTP](./media/durable-functions-overview/async-http-api.png)

Устойчивые функции предоставляют встроенные API-интерфейсы, которые упрощают код, который вы пишете для взаимодействия с длительными выполнениями функций. В этих примерах из краткого руководства ([C#](durable-functions-create-first-csharp.md), [JavaScript](quickstart-js-vscode.md)) показана простая команда REST, которую можно использовать для запуска новых экземпляров функции оркестратора. После запуска экземпляра расширение предоставляет API HTTP веб-перехватчика, которые запрашивают состояние функции оркестратора. В следующем примере показаны команды REST для запуска оркестратора и запроса его состояния. Для ясности в примере опущены некоторые подробности.

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

Так как управление состоянием осуществляется средой выполнения устойчивых функций, вам не нужно реализовывать свой собственный механизм отслеживания состояния.

Несмотря на то что расширение устойчивых функций имеет встроенные веб-перехватчики для управления длительными оркестрациями, вы можете реализовать этот шаблон самостоятельно, используя свои собственные триггеры функций (например, HTTP, очередь или концентратор событий) и привязку `orchestrationClient`. Например, для активации действия прекращения можно использовать сообщение очереди.  Или можно использовать триггер HTTP, защищенный политикой проверки подлинности Azure Active Directory, вместо встроенных веб-перехватчиков с созданным ключом для проверки подлинности.

### <a name="c"></a>C#

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
// HTTP-triggered function to start a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // Function name comes from the request URL.
    // Function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

> [!WARNING]
> При локальной разработке на языке JavaScript необходимо задать для переменной среды `WEBSITE_HOSTNAME` значение `localhost:<port>`, например `localhost:7071`, чтобы использовать методы для `DurableOrchestrationClient`. Дополнительные сведения об этом требовании см. в [описании проблемы на сайте GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

В среде .NET параметр [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` — это значение из выходных данных `orchestrationClient`, которое является частью расширения Устойчивых функций. В JavaScript этот объект возвращается путем вызова `df.getClient(context)`. Эти объекты предоставляют методы для запуска, отправки событий, завершения и запросов новых или существующих экземпляров функций оркестратора.

В предыдущем примере функция, активируемая HTTP, принимает значение `functionName` из входящего URL-адреса и передает это значение в [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Затем этот API привязки [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) возвращает ответ, содержащий заголовок `Location` и дополнительные сведения об экземпляре, которые впоследствии могут использоваться для поиска состояния запущенного экземпляра или его завершения.

## <a name="pattern-4-monitoring"></a>Шаблон 4. Мониторинг

Шаблон монитора представляет собой гибкий *повторяющийся* процесс в рабочем процессе. Например, повторение опроса, пока не будут выполнены определенные условия. Регулярный [триггер таймера](../functions-bindings-timer.md) может активировать простой сценарий, например задание периодической очистки. Но интервал является статическим, что усложняет управление временем существования экземпляра. Устойчивые функции обеспечивают гибкие интервалы повторения, управление временем существования задачи и возможность создавать несколько процессов мониторинга в рамках одной оркестрации.

Примером может служить измененный сценарий асинхронных API-интерфейсов HTTP, приведенный ранее. Вместо предоставления конечной точки внешнему клиенту для мониторинга длительных операций, монитор использует внешнюю конечную точку, ожидая изменения состояния.

![Схема мониторинга](./media/durable-functions-overview/monitor.png)

Устойчивые функции позволяют создать несколько мониторов, которые наблюдают за произвольными конечными точками, используя несколько строк кода. Работа мониторов может приостановиться, если выполнено определенное условие, или ее можно завершить с помощью параметра [DurableOrchestrationClient](durable-functions-instance-management.md). Также их интервал ожидания может измениться в зависимости от некоторых условий (например, экспоненциального откладывания). В следующем коде реализуется простой монитор.

### <a name="c-script"></a>Сценарии C#

```cs
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
            // Perform action when condition met
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform further work here, or let the orchestration end
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform action when condition met
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform further work here, or let the orchestration end
});
```

При получении запроса создается экземпляр оркестрации для указанного идентификатора события. Экземпляр выполняет опрос состояния, пока не выполнится условие или не произойдет выход из цикла. Для управления интервалом опроса используется устойчивый таймер. После этого можно перейти к другим задачам или завершить оркестрацию. Когда `context.CurrentUtcDateTime` (.NET) или `context.df.currentUtcDateTime` (JavaScript) превышает `expiryTime`, монитор останавливается.

## <a name="pattern-5-human-interaction"></a>Шаблон 5. Участие пользователя

Многие процессы требуют некоторого участия пользователя. Трудность, связанная с вовлечением людей в автоматизированный процесс, заключается в том, что пользователи не всегда доступны и не так быстро реагируют, как облачные службы. Автоматические процессы должны учитывать это. Для этого используется время ожидания и логика компенсации.

Одним из примеров бизнес-процесса, который предполагает взаимодействие с пользователями, является процесс утверждения. Например, утверждение от менеджера может потребоваться для отчета о расходах, превышающих определенную сумму. Если менеджер не предоставляет подтверждение в течение 72 часов (возможно, он отправился в отпуск), начинается процесс эскалации, чтобы получить одобрение от кого-то другого (возможно, вышестоящего менеджера).

![Схема участия пользователя](./media/durable-functions-overview/approval.png)

Этот шаблон может быть реализован с помощью функции оркестратора. Оркестратор будет использовать [устойчивый таймер](durable-functions-timers.md), чтобы запросить одобрение и начать эскалацию в случае истечения времени ожидания. Он будет ждать [внешнее событие](durable-functions-external-events.md) в виде уведомления, созданного в результате какого-либо участия пользователя.

### <a name="c-script"></a>Сценарии C#

```cs
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

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```js
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

Устойчивый таймер создается путем вызова `context.CreateTimer` (.NET) или `context.df.createTimer` (JavaScript). Уведомление поступает событию `context.WaitForExternalEvent` (.NET) или `context.df.waitForExternalEvent` (JavaScript). `Task.WhenAny` (.NET) или `context.df.Task.any` (JavaScript) вызывается для того, чтобы решить, следует ли ускорить процесс (сначала время ожидания истечет) или утвердить процесс (утверждение получено до истечения времени ожидания).

Внешний клиент может доставить уведомление о событии функции оркестратора, находящейся в состоянии ожидания, как через [встроенные API-интерфейсы HTTP](durable-functions-http-api.md#raise-event), так и с помощью API [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) из другой функции:

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

## <a name="the-technology"></a>Технология

Расширение устойчивых функций создано на [платформе устойчивых задач](https://github.com/Azure/durabletask), библиотеки с открытым исходным кодом на GitHub для создания оркестраций устойчивых задач. Подобно тому, как функции Azure являются бессерверным развитием веб-заданий Azure, устойчивые функции — это бессерверное развитие платформы устойчивых задач. Платформа устойчивых задач широко используется как в Майкрософт, так и за ее пределами для автоматизации критически важных процессов. Это естественный выбор для бессерверной среды функций Azure.

### <a name="event-sourcing-checkpointing-and-replay"></a>Источники событий, создание контрольных точек и повторное воспроизведение

Функции оркестратора надежно сохраняют свое состояние выполнения с помощью конструктивных шаблонов, также известных как [источник событий](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). Вместо непосредственного сохранения *текущего* состояния оркестрации, устойчивое расширение использует инкрементируемое хранилище для записи *полной последовательности действий*, взятых из функции оркестрации. Это обеспечивает много преимуществ, включая повышение производительности, лучшую масштабируемость и более быструю скорость отклика, по сравнению с выгрузкой полного состояния среды выполнения. Другие преимущества включают в себя итоговую согласованность данных о транзакциях и сохранение всех аудиторских следов и журнала. Аудиторские следы сами по себе обеспечивают надежные компенсирующие действия.

Использование источников событий с помощью этого расширения является прозрачным. На самом деле оркестратор `await` (C#) или `yield` (JavaScript) в функции оркестратора передает управление потоком оркестратора обратно диспетчеру платформы устойчивых задач. Затем диспетчер совершает любые новые действия, запланированные функцией оркестратора (например, вызов одной или нескольких дочерних функций или планирование устойчивого таймера) в хранилище. Это прозрачное действие фиксации добавляется в *журнал выполнения* экземпляра оркестрации. Журнал хранится в таблице хранилища. Затем действие фиксации добавляет сообщения в очередь для планирования фактической работы. На этом этапе функцию оркестрации можно выгрузить из памяти. Выставление счетов для нее останавливается, если вы используете план потребления для решения "Функции Azure".  Если есть еще работа, функция перезапускается, а ее состояние восстанавливается.

Как только функции оркестрации получают больше работы (например, получено ответное сообщение или истек срок действия устойчивого таймера), оркестратор снова активируется и повторно запускает всю функцию с самого начала, чтобы перестроить локальное состояние. Если во время этого воспроизведения код пытается вызвать функцию (или выполнить любую другую асинхронную работу), платформа устойчивых задач просматривает *журнал выполнения* текущей оркестрации. Если он обнаруживает, что [функция действия](durable-functions-types-features-overview.md#activity-functions) уже выполнена и получен определенный результат, он воспроизводит результат этой функции, а код оркестратора продолжает выполняться. Это продолжается до тех пор, пока код функции не дойдет до точки, где он либо завершается, либо планирует новую асинхронную работу.

### <a name="orchestrator-code-constraints"></a>Ограничения кода оркестратора

Поведение воспроизведения ограничивает тип кода, который может быть записан в функцию оркестратора. Например, код оркестратора должен быть детерминированным, так как он будет воспроизводиться множество раз и должен каждый раз выдавать одинаковый результат. Полный список ограничений можно найти в разделе [Ограничения кода оркестратора](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints) статьи **Контрольные точки и воспроизведение в устойчивых функциях (Функции Azure)**.

## <a name="language-support"></a>Поддержка языков

Сейчас для Устойчивых функций поддерживаются только языки C# (решение "Функции" версий 1.x и 2.x), F# и JavaScript (только для решения "Функции" версии 2.x, Устойчивых функций версии 1.7.0 или выше). в частности функций оркестратора и функций действий. Со временем мы добавим поддержку всех языков, поддерживаемых Функциями Azure. Просмотрите [список проблем в репозитории GitHub](https://github.com/Azure/azure-functions-durable-extension/issues) для Функций Azure, чтобы увидеть актуальную информацию о поддерживаемых языках.

## <a name="monitoring-and-diagnostics"></a>Мониторинг и диагностика.

Расширение устойчивых функций автоматически отправляет структурированные данные отслеживания в [Application Insights](../functions-monitoring.md), если приложение-функция настроено с помощью ключа инструментирования Application Insights. Эти данные отслеживания могут использоваться для мониторинга поведения и хода выполнения ваших оркестраций.

Ниже приведен пример того, как выглядят события отслеживания устойчивых функций на портале Application Insights с использованием [аналитики Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).

![Результаты запроса анализа приложения](./media/durable-functions-overview/app-insights-1.png)

В каждой записи журнала содержится много полезных структурированных данных, помещенных в поле `customDimensions`. Ниже приведен пример полностью развернутой записи.

![Поле customDimensions в запросе App Insights](./media/durable-functions-overview/app-insights-2.png)

Из-за поведения воспроизведения диспетчера платформы устойчивых задач возможна избыточность записей журнала повторных действий. Это поможет понять поведение платформы обработки. В статье [Диагностика в устойчивых функциях (Функции Azure)](durable-functions-diagnostics.md) приведены примеры запросов, которые отфильтровывают журналы воспроизведения, чтобы вы могли видеть только журналы "в реальном времени".

## <a name="storage-and-scalability"></a>Хранение и масштабируемость

Расширение устойчивых функций использует очереди, таблицы и большие двоичные объекты службы хранилища Azure, чтобы сохранять состояние журнала выполнения и активировать выполнение функции. Можно использовать учетную запись хранения по умолчанию для приложения-функции или настроить отдельную учетную запись хранения. Возможно, вам понадобится отдельная учетная запись из-за ограничений пропускной способности хранилища. Создаваемый код не должен взаимодействовать с сущностями в этих учетных записях хранения. Сущностями напрямую управляет платформа устойчивых задач.

Функции оркестратора планируют функции действий и получают ответы через сообщения во внутренней очереди. Когда приложение-функция использует план потребления для решения "Функции Azure", эти очереди контролируются с помощью [контроллера масштабирования Функций Azure](../functions-scale.md#how-the-consumption-plan-works) и при необходимости добавляются новые экземпляры вычислений. При масштабировании на нескольких виртуальных машинах функция оркестратора может запускаться на одной виртуальной машине, в то время как функции действий, которые она вызывает, запускаются на нескольких виртуальных машинах. Дополнительные сведения о поведении масштабирования устойчивых функций см. в статье [Производительность и масштабирование в устойчивых функциях (Функции Azure)](durable-functions-perf-and-scale.md).

Хранилище таблиц используется для хранения журнала выполнения для учетных записей оркестратора. Всякий раз, когда экземпляр восстанавливается на определенной виртуальной машине, он извлекает журнал выполнения из хранилища таблиц, чтобы иметь возможность восстановить свое локальное состояние. Одним из удобств наличия журнала в хранилище таблиц является то, что вы можете просмотреть историю ваших оркестраций, используя такие инструменты, как [Обозреватель службы хранилища Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

BLOB-объекты хранилища используются в качестве механизма аренды, чтобы координировать горизонтальное масштабирование экземпляров оркестрации на виртуальных машинах. Они также используются для хранения данных для больших сообщений, которые не могут сохраняться напрямую в таблицах или очередях.

![Снимок экрана обозревателя службы хранилища](./media/durable-functions-overview/storage-explorer.png)

> [!WARNING]
> Хотя просматривать журнал выполнения в службе таблиц более удобно, избегайте использования зависимостей в таблице. Они могут измениться при развитии расширения устойчивых функций.

## <a name="known-issues-and-faq"></a>Известные проблемы и часто задаваемые вопросы

Следует отслеживать все известные проблемы в списке [проблем на GitHub](https://github.com/Azure/azure-functions-durable-extension/issues). Если вы столкнулись с проблемой и не можете найти ее решение на GitHub, откройте новую проблему и укажите ее подробное описание.

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения об Устойчивых функциях в [обзоре типов и возможностей функций в Устойчивых функциях в Azure (Функции Azure)](durable-functions-types-features-overview.md) или...

> [!div class="nextstepaction"]
> [создайте свою первую устойчивую функцию](durable-functions-create-first-csharp.md).