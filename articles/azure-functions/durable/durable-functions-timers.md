---
title: Таймеры в устойчивых функциях — Azure
description: Сведения о том, как реализовать устойчивые таймеры в расширении устойчивых функций для Функций Azure.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0565cc149a36baf31d8516fffcf48b194c465760
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261489"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Таймеры в устойчивых функциях (Функции Azure)

[Устойчивые функции](durable-functions-overview.md) предоставляют *устойчивые таймеры*, которые используются в функциях оркестраторов для реализации задержек или настройки времени ожидания в асинхронных действиях. Устойчивые таймеры следует использовать в функциях оркестраторов вместо `Thread.Sleep` (C#) или `Task.Delay``setTimeout()``setInterval()` (JavaScript).

Вы создаете прочный таймер, `CreateTimer` позвонив `createTimer` в метод (.NET) или метод (JavaScript) [связывания триггера оркестровки.](durable-functions-bindings.md#orchestration-trigger) Метод возвращает задачу, которая завершается в указанную дату и время.

## <a name="timer-limitations"></a>Ограничения таймера

При создании таймера, срок действия которого истекает в 16:30, базовая платформа «Прочная задача» окунает сообщение, которое становится видимым только в 16:30. При запуске в плане потребления функций Azure новое видимое сообщение будет гарантировать, что приложение функции активируется на соответствующем VM.

> [!NOTE]
> * Прочные таймеры в настоящее время ограничены 7 днями. Если необходимы более длительные задержки, их можно смоделировать с помощью AIS-разрегистратора в цикле. `while`
> * Всегда `CurrentUtcDateTime` используйте `DateTime.UtcNow` вместо в `currentUtcDateTime` .NET или вместо `Date.now` или `Date.UTC` в JavaScript вычисления времени пожара для прочных таймеры. Для получения дополнительной [orchestrator function code constraints](durable-functions-code-constraints.md) информации см.

## <a name="usage-for-delay"></a>Использование для задержки

В следующем примере показано, как использовать устойчивые таймеры для задержки выполнения. Примером является выдача уведомления о выставлении счетов каждый день в течение 10 дней.

# <a name="c"></a>[C #](#tab/csharp)

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
> Предыдущий пример C-примера нацелен на функции durable Functions 2.x. Для долгосрочных функций 1.x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext`. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

---

> [!WARNING]
> Избегайте бесконечных циклов в функциях оркестраторов. Сведения о том, как безопасно и эффективно реализовать сценарии с бесконечным циклом, см. в описании [внешних оркестраций](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Использование для установки времени ожидания

В этом примере показано, как использовать устойчивые таймеры для реализации времени ожидания.

# <a name="c"></a>[C #](#tab/csharp)

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
> Предыдущий пример C-примера нацелен на функции durable Functions 2.x. Для долгосрочных функций 1.x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext`. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

> [!WARNING]
> Используйте `CancellationTokenSource` (.NET) `cancel()` или позвоните по возвращенному `TimerTask` (JavaScript), чтобы отменить прочный таймер, если ваш код не будет ждать его завершения. Рамочная задача длительной задачи не изменит статус оркестровки на "завершенный" до тех пор, пока не будут выполнены или отменены все невыполненные задачи.

Этот механизм отмены не прекращает выполнение функции в процессе выполнения или выполнения суб-оркестров. Вместо этого он просто позволяет функции оркестратора игнорировать результат и двигаться дальше. Если приложение функции использует план Потребления, вам по-прежнему будет выставлен счет за любое время и память, потребляемую заброшенной функцией активности. По умолчанию для функций, выполняемых в плане потребления, устанавливается время ожидания в пять минут. При превышении этого ограничения узел Функций Azure перезапускается для остановки выполнения всех процессов и предотвращения неконтролируемого выставления счетов. [Время ожидания функции можно настроить](../functions-host-json.md#functiontimeout).

Более подробный пример того, как реализовать тайм-ауты в функциях оркестратора, с [&м.](durable-functions-phone-verification.md)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Сведения о том, как вызывать и обрабатывать внешние события](durable-functions-external-events.md)
