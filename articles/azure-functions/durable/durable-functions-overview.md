---
title: Обзор устойчивых функций — Azure
description: Общие сведения о расширении устойчивых функций для Функций Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/07/2019
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: a917a823d47d6a072cf5a3ee5d636b432913df9a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299444"
---
# <a name="what-are-durable-functions"></a>Что такое Устойчивые функции?

*Устойчивые функции* — это расширение [Функций Azure](../functions-overview.md), которое позволяет писать функции с отслеживанием состояния в беcсерверной вычислительной среде. Расширение позволяет определять рабочие процессы с отслеживанием состояния, создавая [*функции оркестратора*](durable-functions-orchestrations.md), и сущности с отслеживанием состояния, создавая [*функции сущностей*](durable-functions-entities.md) с помощью модели программирования Функций Azure. В фоновом режиме расширение автоматически управляет состоянием, создает контрольные точки и перезагружается, позволяя вам сосредоточиться на бизнес-логике.

## <a name="language-support"></a>Поддерживаемые языки

Устойчивые функции в настоящее время поддерживают следующие языки.

* **C#** : обе [предварительно скомпилированные библиотеки классов](../functions-dotnet-class-library.md) и [сценарий C#](../functions-reference-csharp.md).
* **F#** : предварительно скомпилированные библиотеки классов и сценарий F#. Сценарий F# (.fsx) поддерживается только в среде выполнения Функций Azure версии 1.x.
* **JavaScript**: поддерживается только для версии 2.x среды выполнения Функций Azure. Требуется расширение устойчивых функций версии 1.7.0 или более поздней версии. 

Целью устойчивых функций является поддержка всех [языков Функций Azure](../supported-languages.md). См. [здесь](https://github.com/Azure/azure-functions-durable-extension/issues) список проблем Устойчивых функций для получения последних сведений о состоянии работы для поддержки дополнительных языков.

Подобно Функциям Azure, доступны шаблоны, которые помогут вам в разработке устойчивых функций с помощью [Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md) и [портала Azure](durable-functions-create-portal.md).

## <a name="application-patterns"></a>Шаблоны приложений

Основной вариант использования Устойчивых функций — это упрощение комплексных требований координации с отслеживанием состояния в безсерверных приложениях. В следующих разделах описываются типичные шаблоны приложений, в которых можно эффективно использовать Устойчивые функции.

* [Цепочка функций](#chaining)
* [Развертывание и объединение](#fan-in-out)
* [Асинхронные API-интерфейсы HTTP](#async-http)
* [Мониторинг](#monitoring)
* [Участие пользователя](#human)
* [Агрегатор](#aggregator)

### <a name="chaining"></a>Шаблон 1. Цепочка функций

В шаблоне цепочки функций последовательность функций выполняется в определенном порядке. В этом шаблоне выходные данные одной функции применяются к входным данным другой.

![Схема шаблона цепочки функций](./media/durable-functions-concepts/function-chaining.png)

Устойчивые функции можно использовать для реализации шаблона цепочки функций, как показано в следующем примере:

#### <a name="c"></a>C#

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return    yield context.df.callActivity("F4", z);
});
```

В этом примере значения `F1`, `F2`, `F3` и `F4` являются именами других функций в приложении-функции. Поток управления можно реализовать с помощью обычных принудительных конструкций программирования. Код выполняется сверху вниз. Код может включать в себя имеющуюся семантику языка потока управления, такую ​​как условные обозначения и циклы. Вы можете включить логику обработки ошибок в блоках `try`/`catch`/`finally`.

Вы можете использовать параметр `context` DurableOrchestrationContext \(.NET\) и объект `context.df` (JavaScript) для вызова других функций по имени, передачи параметров и возврата выходных данных функции. Каждый раз, когда код вызывает `await` (C#) или `yield` (JavaScript), платформа Устойчивых функций создает контрольные точки выполнения текущего экземпляра функции. Если процесс или виртуальная машина перезапускается во время выполнения, экземпляр функции возобновляется из предыдущего вызова `await` или `yield`. Этот процесс описан в следующем разделе Шаблон 2: развертывание и объединение.

> [!NOTE]
> Объект `context` в JavaScript представляет не только параметр [DurableOrchestrationContext], а весь [контекст функции](../functions-reference-node.md#context-object).

### <a name="fan-in-out"></a>Шаблон 2. Развертывание и объединение

В шаблоне развертывания и объединения вы выполняете параллельно несколько функций, а затем ожидаете их завершения. Часто некоторые агрегирования завершаются по результатам, возвращаемым функциями.

![Схема шаблона развертывания и объединения](./media/durable-functions-concepts/fan-out-fan-in.png)

При использовании обычных функций вы можете выполнить развертывание за счет отправки функцией нескольких сообщений в очередь. Войти обратно гораздо сложнее. Чтобы выполнить объединение в обычной функции, напишите код для отслеживания момента, когда функции, активируемые очередью, завершаются, а затем их выходные значения сохраняются.

Расширение "Устойчивые функции" обрабатывает этот шаблон с помощью относительно простого кода:

#### <a name="c"></a>C#

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
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

Процесс развертывания распределяется по нескольким экземплярам функции `F2`. И отслеживается с использованием динамического списка задач. Вызывается API .NET `Task.WhenAll` или API JavaScript `context.df.Task.all` для ожидания завершения всех вызванных функций. Затем выходные данные функции `F2` агрегируются из списка динамических задач и передаются функции `F3`.

Автоматическое создание контрольных точек, которое происходит при вызове `await` или `yield` к `Task.WhenAll` или `context.df.Task.all`, гарантирует, что возможный сбой или перезагрузка во время выполнения не потребуют перезапуска уже завершенной задачи.

> [!NOTE]
> В редких случаях может произойти сбой в окне после завершения функции действия, но до того, как ее выполнение будет сохранено в журнале оркестрации. В этом случае функция действия будет повторно выполнена с начала после восстановления процесса.

### <a name="async-http"></a>Шаблон 3. Асинхронные API-интерфейсы HTTP

Шаблон асинхронного API HTTP решает проблему координации состояния длительных операций с внешними клиентами. Распространенный способ реализации этого шаблона — наличие триггера конечной точки HTTP для выполнения длительного действия. Затем перенаправьте клиент на конечную точку состояния, которую клиент опрашивает для получения сведений о завершении операции.

![Схема шаблона API HTTP](./media/durable-functions-concepts/async-http-api.png)

Устойчивые функции предоставляют **встроенную поддержку** для этого шаблона, упрощая или даже удаляя код, который вам нужно написать для взаимодействия с длительными выполнениями функций. Например, в примерах из краткого руководства по Устойчивым функциям ([C#](durable-functions-create-first-csharp.md) и [JavaScript](quickstart-js-vscode.md)) показана простая команда REST, которую вы можете использовать для запуска новых экземпляров функции оркестратора. После запуска экземпляра расширение предоставляет API HTTP веб-перехватчика, которые запрашивают состояние функции оркестратора. 

В следующем примере показаны команды REST, которые запускают оркестратор и запрашивают его состояние. Для ясности в примере опущены некоторые данные протокола.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Так как управление состоянием осуществляется средой выполнения Устойчивых функций, вам не нужно реализовывать собственный механизм отслеживания состояния.

Расширение "Устойчивые функции" предоставляет встроенные API HTTP, которые управляют длительными оркестрациями. Вы также можете реализовать этот шаблон самостоятельно, используя собственные триггеры функций (например, HTTP, очередь или Центры событий Azure) и [привязку клиента оркестрации](durable-functions-bindings.md#orchestration-client). Например, для активации действия прекращения можно использовать сообщение очереди. Или можно использовать триггер HTTP, защищенный политикой проверки подлинности Azure Active Directory, вместо встроенных API-интерфейсов HTTP с созданным ключом для проверки подлинности.

Дополнительные сведения см. в статье о [функциях HTTP](durable-functions-http-features.md), в которой объясняется, как можно предоставлять асинхронные длительные процессы по протоколу HTTP с помощью расширения "Устойчивые функции".

### <a name="monitoring"></a>Шаблон 4. Мониторинг

Шаблон мониторинга представляет собой гибкий, повторяющийся процесс в рабочем процессе. Например, повторение опроса, пока не будут выполнены определенные условия. Вы можете использовать регулярный [триггер таймера](../functions-bindings-timer.md) для активации основного сценария, например задание периодической очистки. Но интервал является статическим, что усложняет управление временем существования экземпляра. Вы можете использовать Устойчивые функции, чтобы создать гибкие интервалы повторения, управлять временем существования задачи и создавать несколько процессов мониторинга в рамках одной оркестрации.

Примером шаблона мониторинга является измененный сценарий асинхронных API-интерфейсов HTTP, приведенный ранее. Вместо предоставления конечной точки внешнему клиенту для мониторинга длительной операции, мониторинг использует внешнюю конечную точку, а затем ожидает изменение состояния.

![Схема шаблона мониторинга](./media/durable-functions-concepts/monitor.png)

Благодаря нескольким строкам кода можно использовать Устойчивые функции, чтобы создать несколько мониторингов, которые наблюдают за произвольными конечными точками. Работа мониторингов может приостановиться, если выполнено условие, или ее можно завершить с помощью параметра [DurableOrchestrationClient](durable-functions-instance-management.md). Можно изменить интервал мониторинга `wait` в зависимости от определенного условия (например, экспоненциальной задержки). 

В следующем коде реализуется простой мониторинг:

#### <a name="c"></a>C#

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

При получении запроса создается экземпляр оркестрации для указанного идентификатора события. Экземпляр выполняет опрос состояния, пока не выполнится условие или не произойдет выход из цикла. Для управления интервалом опроса используется устойчивый таймер. После этого можно перейти к другим задачам или завершить оркестрацию. Когда `context.CurrentUtcDateTime` (.NET) или `context.df.currentUtcDateTime` (JavaScript) превышает значение `expiryTime`, мониторинг останавливается.

### <a name="human"></a>Шаблон 5. Участие пользователя

Многие автоматизированные процессы требуют некоторого участия пользователя. Трудность вовлечения людей в автоматизированный процесс заключается в том, что пользователи не так доступны и не так быстро реагируют, как облачные службы. Автоматизированный процесс должен учитывать это. Для этого используется время ожидания и логика компенсации.

Процесс утверждения является примером бизнес-процесса, который предполагает взаимодействие с пользователями. Утверждение от менеджера может потребоваться для отчета о расходах, превышающих определенную денежную сумму. Если менеджер не подтверждает отчет о расходах в течение 72 часов (возможно, он отправился в отпуск), начинается процесс эскалации, чтобы получить одобрение от кого-то другого (возможно, вышестоящего менеджера).

![Схема шаблона участия пользователя](./media/durable-functions-concepts/approval.png)

Этот шаблон в примере можно реализовать с помощью функции оркестратора. Оркестратор использует [устойчивый таймер](durable-functions-timers.md), чтобы запросить одобрение. Оркестратор начинает эскалацию, если истекло время ожидания. Он ожидает [внешнее событие](durable-functions-external-events.md) в виде уведомления, созданного в результате участия пользователя.

В этих примерах создается процесс утверждения для демонстрации шаблона участия пользователя:

#### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
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
            await context.CallActivityAsync("Escalate", null);
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

Для создания устойчивого таймера вызовите `context.CreateTimer` (.NET) или `context.df.createTimer` (JavaScript). Уведомление поступает событию `context.WaitForExternalEvent` (.NET) или `context.df.waitForExternalEvent` (JavaScript). Затем `Task.WhenAny` (.NET) или `context.df.Task.any` (JavaScript) вызывается для того, чтобы решить, следует ли ускорить процесс (сначала время ожидания истечет) или утвердить процесс (утверждение получено до истечения времени ожидания).

Внешний клиент может доставить уведомление о событии функции оркестратора, находящейся в состоянии ожидания, как через [встроенные API-интерфейсы HTTP](durable-functions-http-api.md#raise-event), так и с помощью API [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) из другой функции:

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
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

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

### <a name="aggregator"></a>Шаблон 6. Агрегатор (предварительная версия)

Шестой шаблон заключается в статистической обработке данных событий за определенный период времени в одну доступную для адресации *сущность*. В этом шаблоне данные, для которых выполняется агрегирование, могут поступать из нескольких источников, могут быть доставлены пакетами или разбросаны по длительным временным периодам. Агрегатору может потребоваться выполнить действия над данными событий по мере их поступления, а внешним клиентам может потребоваться запросить агрегированные данные.

![Схема агрегатора](./media/durable-functions-concepts/aggregator.png)

Сложности при реализации этого шаблона с обычными функциями без отслеживания состояния заключаются в том, что управление параллелизмом крайне усложняется. Нужно не только беспокоиться о том, что несколько потоков одновременно изменяют одни и те же данные, также важно и то, чтобы агрегатор одновременно выполнялся только на одной виртуальной машине.

Используя [функцию устойчивой сущности](durable-functions-preview.md#entity-functions), можно легко реализовать этот шаблон как единую функцию.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += amount;
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

Устойчивые сущности также можно моделировать как классы .NET. Эта модель может быть полезной, если список операций является фиксированным и становится большим. Следующий пример представляет собой эквивалентную реализацию сущности `Counter` с помощью классов и методов .NET.

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

Клиенты могут поставить в очередь *операции* (называемые также "сигнализацией") для функции сущности, использующей [привязку клиента сущности](durable-functions-bindings.md#entity-client).

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

Динамически создаваемые прокси-серверы также доступны для сигнализации сущностям типобезопасным способом. Кроме сигнализации, клиенты также могут запрашивать состояние функции сущности с помощью [типобезопасных методов](durable-functions-bindings.md#entity-client-usage) в привязке клиента оркестрации.

> [!NOTE]
> Функции сущностей на данный момент доступны только в .NET как часть [Устойчивых функций версии 2.0 (предварительная версия)](durable-functions-preview.md).

## <a name="the-technology"></a>Технология

Расширение "Устойчивые функции" создано на [платформе устойчивых задач](https://github.com/Azure/durabletask), библиотеки с открытым кодом на GitHub для создания рабочих процессов в коде. Подобно тому, как Функции Azure являются бессерверным развитием веб-заданий Azure, Устойчивые функции — это бессерверное развитие платформы устойчивых задач. Платформа устойчивых задач широко используется как корпорацией Майкрософт, так и другими организациями для автоматизации критически важных процессов. Это естественный выбор для бессерверной среды функций Azure.

## <a name="code-constraints"></a>Ограничения кода

Чтобы обеспечить надежность и гарантировать длительное выполнение, в функциях оркестратора предусмотрен набор правил кода, которым нужно следовать. Дополнительные сведения см. в статье [Orchestrator function code constraints](durable-functions-code-constraints.md) (Ограничения кода функции оркестратора).

## <a name="billing"></a>Выставление счетов

Счета за устойчивые функции выставляются так же, как и за Функции Azure. Дополнительные сведения см. на странице [цен на Функции Azure](https://azure.microsoft.com/pricing/details/functions/). При выполнении функций оркестратора в рамках [плана потребления](../functions-scale.md#consumption-plan) Функций Azure необходимо учитывать некоторые особенности при выставлении счетов. Дополнительные сведения об этом см. в статье [Durable Functions Billing](durable-functions-billing.md) (Выставление счетов за Устойчивые функции).

## <a name="jump-right-in"></a>Приступайте к работе

Вы можете начать работу с Устойчивыми функциями менее чем за 10 минут, просмотрев одно из следующих кратких руководств по конкретным языкам.

* [Создание устойчивой функции на C#](durable-functions-create-first-csharp.md)
* [Создание устойчивой функции с помощью JavaScript](quickstart-js-vscode.md)

В обоих кратких руководствах вы локально создаете и тестируете устойчивую функцию "Hello world". Затем вы опубликуете код функции в Azure. Функция, которую вы создаете, организовывает и объединяет в цепочку вызовы других функций.

## <a name="learn-more"></a>Подробнее

В следующем видео показаны преимущества Устойчивых функций.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Более подробное обсуждение Устойчивых функций и базовой технологии см. в следующем видео (оно ориентировано на .NET, но понятия также применимы и к другим поддерживаемым языкам):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Так как Устойчивые функции — это дополнительное расширение решения [Функции Azure](../functions-overview.md), оно подходит не для всех приложений. Сравнение с другими технологиями оркестрации Azure см. в разделе [Сравнение служб "Функции Azure" и Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Durable Functions types and features (Azure Functions)](durable-functions-types-features-overview.md) (Типы и возможности Устойчивых функций (Функции Azure))
