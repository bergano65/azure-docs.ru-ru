---
title: Таймеры в устойчивых функциях — Azure
description: Сведения о том, как реализовать устойчивые таймеры в расширении устойчивых функций для Функций Azure.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: c477a81801c1345d5be5f5f45419bb4776c875e0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231279"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Таймеры в устойчивых функциях (Функции Azure)

[Устойчивые функции](durable-functions-overview.md) предоставляют *устойчивые таймеры*, которые используются в функциях оркестраторов для реализации задержек или настройки времени ожидания в асинхронных действиях. Устойчивые таймеры следует использовать в функциях оркестраторов вместо `Thread.Sleep` (C#) или `Task.Delay``setTimeout()``setInterval()` (JavaScript).

You create a durable timer by calling the `CreateTimer` (.NET) method or the `createTimer` (JavaScript) method of the [orchestration trigger binding](durable-functions-bindings.md#orchestration-trigger). The method returns a task that completes on a specified date and time.

## <a name="timer-limitations"></a>Ограничения таймера

When you create a timer that expires at 4:30 pm, the underlying Durable Task Framework enqueues a message that becomes visible only at 4:30 pm. When running in the Azure Functions Consumption plan, the newly visible timer message will ensure that the function app gets activated on an appropriate VM.

> [!NOTE]
> * Durable timers are currently limited to 7 days. If longer delays are needed, they can be simulated using the timer APIs in a `while` loop.
> * Always use `CurrentUtcDateTime` instead of `DateTime.UtcNow` in .NET or `currentUtcDateTime` instead of `Date.now` or `Date.UTC` in JavaScript when computing the fire time for durable timers. For more information, see the [orchestrator function code constraints](durable-functions-code-constraints.md) article.

## <a name="usage-for-delay"></a>Использование для задержки

В следующем примере показано, как использовать устойчивые таймеры для задержки выполнения. The example is issuing a billing notification every day for 10 days.

### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> The previous C# example targets Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript (только Функции 2.0)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

> [!WARNING]
> Избегайте бесконечных циклов в функциях оркестраторов. Сведения о том, как безопасно и эффективно реализовать сценарии с бесконечным циклом, см. в описании [внешних оркестраций](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Использование для установки времени ожидания

В этом примере показано, как использовать устойчивые таймеры для реализации времени ожидания.

### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> The previous C# example targets Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript (только Функции 2.0)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

> [!WARNING]
> Use a `CancellationTokenSource` to cancel a durable timer (.NET) or call `cancel()` on the returned `TimerTask` (JavaScript) if your code will not wait for it to complete. The Durable Task Framework will not change an orchestration's status to "completed" until all outstanding tasks are completed or canceled.

This cancellation mechanism doesn't terminate in-progress activity function or sub-orchestration executions. Вместо этого он просто позволяет функции оркестратора игнорировать результат и двигаться дальше. If your function app uses the Consumption plan, you'll still be billed for any time and memory consumed by the abandoned activity function. По умолчанию для функций, выполняемых в плане потребления, устанавливается время ожидания в пять минут. При превышении этого ограничения узел Функций Azure перезапускается для остановки выполнения всех процессов и предотвращения неконтролируемого выставления счетов. [Время ожидания функции можно настроить](../functions-host-json.md#functiontimeout).

For a more in-depth example of how to implement timeouts in orchestrator functions, see the [Human Interaction & Timeouts - Phone Verification](durable-functions-phone-verification.md) article.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Сведения о том, как вызывать и обрабатывать внешние события](durable-functions-external-events.md)
