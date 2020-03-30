---
title: Обработка ошибок в устойчивых функциях — Azure
description: Сведения о том, как обрабатывать ошибки в расширении устойчивых функций для Функций Azure.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 447b3dcf5040835f5a853beff68bde794ece51f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277860"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Обработка ошибок в устойчивых функциях (Функции Azure)

Прочеденная оркестровка функций реализована в коде и может использовать встроенные функции обработки ошибок на языке программирования. Там действительно нет каких-либо новых концепций, вам нужно научиться добавлять обработки ошибок и компенсации в оркестровки. Однако следует помнить о некоторых особенностях поведения.

## <a name="errors-in-activity-functions"></a>Ошибки в функциях действий

Любое исключение, которое брошено в функции действия, возвращается `FunctionFailedException`к функции оркестратора и выбрасывается как . Вы можете написать код для обработки и компенсации ошибок, соответствующий вашим потребностям в функции оркестратора.

Например, рассмотрим следующую функцию оркестратора, которая перемещает средства с одного счета на другой:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> Предыдущие примеры СЗ относятся к долгосрочным функциям 2.x. Для долгосрочных функций 1.x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext`. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

---

Если первый вызов функции **CreditAccount** не удается, функция оркестратора компенсируется за счет зачисления средств обратно на исходный счет.

## <a name="automatic-retry-on-failure"></a>Автоматическое повторение попыток при сбое

При вызове функций действий или функций суборкестрации можно указать автоматическую политику повтора. В следующем примере предпринимается попытка вызова функции до трех раз с ожиданием в течение 5 секунд между попытками:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> Предыдущие примеры СЗ относятся к долгосрочным функциям 2.x. Для долгосрочных функций 1.x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext`. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

---

Вызов функции действия в предыдущем примере использует параметр для настройки политики автоматической повторной попытки. Существует несколько вариантов настройки политики автоматической повторной попытки:

* **Max number of attempts** (Максимальное число попыток): максимальное число повторных попыток.
* **First retry interval** (Интервал до первого повтора): время ожидания перед первой повторной попыткой.
* **Backoff coefficient** (Коэффициент отсрочки): коэффициент, позволяющий определить степень увеличения отсрочки. По умолчанию равен 1.
* **Max retry interval** (Максимальный интервал повтора): максимальное время ожидания между повторными попытками.
* **Retry timeout** (Время ожидания повтора): максимальное время, отведенное на выполнение повторных попыток. Поведение по умолчанию — бесконечное повторение.
* **Ручка**: Можно указать обратный вызов, определяемый пользователем, чтобы определить, следует ли повторить функцию.

## <a name="function-timeouts"></a>Время ожидания функций

Вы можете отказаться от вызова функции в функции оркестратора, если это занимает слишком много времени, чтобы завершить. Правильный способ сделать это в настоящее время — создать [устойчивый таймер](durable-functions-timers.md), используя `context.CreateTimer` (.NET) или `context.df.createTimer` (JavaScript) в сочетании с `Task.WhenAny` (.NET) или `context.df.Task.any` (JavaScript), как показано в следующем примере:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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
> Предыдущие примеры СЗ относятся к долгосрочным функциям 2.x. Для долгосрочных функций 1.x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext`. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

---

> [!NOTE]
> Этот механизм фактически не прекращает текущее выполнение функции действия. Вместо этого он просто позволяет функции оркестратора игнорировать результат и двигаться дальше. Дополнительные сведения см. в документации по [таймерам](durable-functions-timers.md#usage-for-timeout).

## <a name="unhandled-exceptions"></a>Необработанные исключения

Если функция оркестратора завершается сбоем с необработанным исключением, сведения об этом исключении регистрируются в журнале, и экземпляр завершает работу с состоянием `Failed`.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Узнайте о вечных оркестровке](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Дополнительные сведения для диагностики неполадок](durable-functions-diagnostics.md)
