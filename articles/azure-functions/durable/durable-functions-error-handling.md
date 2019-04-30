---
title: Обработка ошибок в устойчивых функциях — Azure
description: Сведения о том, как обрабатывать ошибки в расширении устойчивых функций для Функций Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: f3d7f916d31a03d7b868749026f541dd646459f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60732524"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Обработка ошибок в устойчивых функциях (Функции Azure)

Оркестрации устойчивых функций реализованы в коде и могут использовать возможности языка программирования по обработке ошибок. С учетом этого при внедрении в оркестрации обработки и компенсации ошибок не требуется изучать новые концепции. Однако следует помнить о некоторых особенностях поведения.

## <a name="errors-in-activity-functions"></a>Ошибки в функциях действий

Любое исключение, возникающее в функции действия, маршалируется обратно в функцию оркестратора и выдается как `FunctionFailedException`. Вы можете написать код для обработки и компенсации ошибок, соответствующий вашим потребностям в функции оркестратора.

Например, рассмотрим следующую функцию оркестратора, которая перемещает средства с одного счета на другой:

### <a name="c"></a>C#

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

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

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

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

Если вызов функции **CreditAccount** завершается с ошибкой для целевого счета, функция оркестратора компенсирует это, кредитуя денежные средства обратно на исходный счет.

## <a name="automatic-retry-on-failure"></a>Автоматическое повторение попыток при сбое

При вызове функций действий или функций суборкестрации можно указать автоматическую политику повтора. В следующем примере предпринимается попытка вызова функции до трех раз с ожиданием в течение 5 секунд между попытками:

### <a name="c"></a>C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

API `CallActivityWithRetryAsync` (.NET) или `callActivityWithRetry` (JavaScript) принимает параметр `RetryOptions`. В вызовах суборкестрации через API `CallSubOrchestratorWithRetryAsync` (.NET) или `callSubOrchestratorWithRetry` (JavaScript) можно использовать эти же политики повтора.

Существует несколько параметров настройки политики автоматического повтора. Среди них следующие страны:

* **Max number of attempts** (Максимальное число попыток). Максимальное число повторных попыток.
* **First retry interval** (Интервал до первого повтора). Время ожидания перед первой повторной попыткой.
* **Backoff coefficient** (Коэффициент отсрочки). Коэффициент, позволяющий определить степень увеличения отсрочки. По умолчанию равен 1.
* **Max retry interval** (Максимальный интервал повтора). Максимальное время ожидания между повторными попытками.
* **Retry timeout** (Время ожидания повтора). Максимальное время, отведенное на выполнение повторных попыток. Поведение по умолчанию — бесконечное повторение.
* **Handle** (Обработка). Вы можете указать определенный пользователем обратный вызов, который определяет, следует ли повторять попытку вызова функции.

## <a name="function-timeouts"></a>Время ожидания функций

Вы можете прекратить вызов функции внутри функции оркестратора, если его завершение занимает слишком много времени. Правильный способ сделать это в настоящее время — создать [устойчивый таймер](durable-functions-timers.md), используя `context.CreateTimer` (.NET) или `context.df.createTimer` (JavaScript) в сочетании с `Task.WhenAny` (.NET) или `context.df.Task.any` (JavaScript), как показано в следующем примере:

### <a name="c"></a>C#

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
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

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

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

> [!NOTE]
> Этот механизм фактически не прекращает текущее выполнение функции действия. Вместо этого он просто позволяет функции оркестратора игнорировать результат и двигаться дальше. Дополнительные сведения см. в документации по [таймерам](durable-functions-timers.md#usage-for-timeout).

## <a name="unhandled-exceptions"></a>Необработанные исключения

Если функция оркестратора завершается сбоем с необработанным исключением, сведения об этом исключении регистрируются в журнале, и экземпляр завершает работу с состоянием `Failed`.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Дополнительные сведения для диагностики неполадок](durable-functions-diagnostics.md)
