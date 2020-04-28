---
title: Таймеры в устойчивых функциях — Azure
description: Сведения о том, как реализовать устойчивые таймеры в расширении устойчивых функций для Функций Azure.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0565cc149a36baf31d8516fffcf48b194c465760
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76261489"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Таймеры в устойчивых функциях (Функции Azure)

[Устойчивые функции](durable-functions-overview.md) предоставляют *устойчивые таймеры*, которые используются в функциях оркестраторов для реализации задержек или настройки времени ожидания в асинхронных действиях. Устойчивые таймеры следует использовать в функциях оркестраторов вместо `Thread.Sleep` (C#) или `Task.Delay``setTimeout()``setInterval()` (JavaScript).

Устойчивый таймер создается путем вызова метода `CreateTimer` (.NET) или метода `createTimer` (JavaScript) [привязки триггера оркестрации](durable-functions-bindings.md#orchestration-trigger). Метод возвращает задачу, которая завершается в указанные дату и время.

## <a name="timer-limitations"></a>Ограничения таймера

При создании таймера, срок действия которого истекает до 4:30 PM, базовая платформа устойчивых задач ставит в очередь сообщение, которое станет видимым только в 4:30 РМ. При запуске в плане потребления функций Azure вновь видимое сообщение таймера обеспечит активацию приложения функции на соответствующей виртуальной машине.

> [!NOTE]
> * Устойчивые таймеры в настоящее время ограничены 7 днями. Если требуется больше задержек, их можно имитировать с помощью API- `while` интерфейсов таймера в цикле.
> * При вычислении времени срабатывания устойчивых таймеров всегда `CurrentUtcDateTime` используйте вместо `DateTime.UtcNow` в .NET или `currentUtcDateTime` вместо `Date.now` или `Date.UTC` в JavaScript. Дополнительные сведения см. в статье [ограничения кода функции Orchestrator](durable-functions-code-constraints.md) .

## <a name="usage-for-delay"></a>Использование для задержки

В следующем примере показано, как использовать устойчивые таймеры для задержки выполнения. В этом примере выдается уведомление о выставлении счетов каждый день в течение 10 дней.

# <a name="c"></a>[C#](#tab/csharp)

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
> Предыдущий пример на C# предназначен для Устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать `DurableOrchestrationContext` вместо. `IDurableOrchestrationContext` Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="c"></a>[C#](#tab/csharp)

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
> Предыдущий пример на C# предназначен для Устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать `DurableOrchestrationContext` вместо. `IDurableOrchestrationContext` Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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
> Используйте `CancellationTokenSource` (.NET) или вызов `cancel()` в возвращенном `TimerTask` коде (JavaScript), чтобы отменить устойчивый таймер, если ваш код не будет ждать его завершения. Платформа устойчивых задач не изменит состояние оркестрации на "завершено" до тех пор, пока все невыполненные задачи не будут завершены или отменены.

Этот механизм отмены не прерывает выполняющиеся действия по выполнению функций или подorchestration. Вместо этого он просто позволяет функции оркестратора игнорировать результат и двигаться дальше. Если приложение-функция использует план потребления, плата за все время и память, потребляемые прерванной функцией действия, будет взиматься. По умолчанию для функций, выполняемых в плане потребления, устанавливается время ожидания в пять минут. При превышении этого ограничения узел Функций Azure перезапускается для остановки выполнения всех процессов и предотвращения неконтролируемого выставления счетов. [Время ожидания функции можно настроить](../functions-host-json.md#functiontimeout).

Более подробный пример реализации времени ожидания в функциях Orchestrator см. в статье [взаимодействие с человеком & время ожидания — Проверка телефона](durable-functions-phone-verification.md) .

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Сведения о том, как вызывать и обрабатывать внешние события](durable-functions-external-events.md)
