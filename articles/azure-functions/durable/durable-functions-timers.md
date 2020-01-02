---
title: Таймеры в устойчивых функциях — Azure
description: Сведения о том, как реализовать устойчивые таймеры в расширении устойчивых функций для Функций Azure.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 463d5e6c253643c82935c82c7dee5996c8e44b5f
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706104"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Таймеры в устойчивых функциях (Функции Azure)

[Устойчивые функции](durable-functions-overview.md) предоставляют *устойчивые таймеры*, которые используются в функциях оркестраторов для реализации задержек или настройки времени ожидания в асинхронных действиях. Устойчивые таймеры следует использовать в функциях оркестраторов вместо `Thread.Sleep` (C#) или `Task.Delay``setTimeout()``setInterval()` (JavaScript).

Устойчивый таймер создается путем вызова метода `CreateTimer` (.NET) или метода `createTimer` (JavaScript) [привязки триггера оркестрации](durable-functions-bindings.md#orchestration-trigger). Метод возвращает задачу, которая завершается в указанные дату и время.

## <a name="timer-limitations"></a>Ограничения таймера

При создании таймера, срок действия которого истекает до 4:30 PM, базовая платформа устойчивых задач ставит в очередь сообщение, которое станет видимым только в 4:30 РМ. При запуске в плане потребления функций Azure вновь видимое сообщение таймера обеспечит активацию приложения функции на соответствующей виртуальной машине.

> [!NOTE]
> * Устойчивые таймеры в настоящее время ограничены 7 днями. Если требуется больше задержек, их можно имитировать с помощью API-интерфейсов таймера в цикле `while`.
> * Всегда используйте `CurrentUtcDateTime` вместо `DateTime.UtcNow` в .NET или `currentUtcDateTime` вместо `Date.now` или `Date.UTC` в JavaScript при вычислении времени срабатывания для устойчивых таймеров. Дополнительные сведения см. в статье [ограничения кода функции Orchestrator](durable-functions-code-constraints.md) .

## <a name="usage-for-delay"></a>Использование для задержки

В следующем примере показано, как использовать устойчивые таймеры для задержки выполнения. В этом примере выдается уведомление о выставлении счетов каждый день в течение 10 дней.

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
> Предыдущий C# пример предназначен для устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext`. Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>JavaScript (только Функции 2.0)

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
> Предыдущий C# пример предназначен для устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext`. Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

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
> Используйте `CancellationTokenSource`, чтобы отменить устойчивый таймер (.NET) или вызвать `cancel()` на возвращенном `TimerTask` (JavaScript), если код не будет ждать его завершения. Платформа устойчивых задач не изменит состояние оркестрации на "завершено" до тех пор, пока все невыполненные задачи не будут завершены или отменены.

Этот механизм отмены не прерывает выполняющиеся действия по выполнению функций или подorchestration. Вместо этого он просто позволяет функции оркестратора игнорировать результат и двигаться дальше. Если приложение-функция использует план потребления, плата за все время и память, потребляемые прерванной функцией действия, будет взиматься. По умолчанию для функций, выполняемых в плане потребления, устанавливается время ожидания в пять минут. При превышении этого ограничения узел Функций Azure перезапускается для остановки выполнения всех процессов и предотвращения неконтролируемого выставления счетов. [Время ожидания функции можно настроить](../functions-host-json.md#functiontimeout).

Более подробный пример реализации времени ожидания в функциях Orchestrator см. в статье [взаимодействие с человеком & время ожидания — Проверка телефона](durable-functions-phone-verification.md) .

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Сведения о том, как вызывать и обрабатывать внешние события](durable-functions-external-events.md)
