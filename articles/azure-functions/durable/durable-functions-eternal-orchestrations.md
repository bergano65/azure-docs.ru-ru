---
title: Нескончаемые оркестрации в устойчивых функциях — Azure
description: Узнайте, как реализовать нескончаемые оркестрации с помощью расширения устойчивых функций для Функций Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 8d28ae18c44c434dba053b23a60eb78728f8d8e0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232910"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Нескончаемые оркестрации в устойчивых функциях (Функции Azure)

*Нескончаемые оркестрации* — это функции оркестрации, которые никогда не останавливаются. Они позволяют создать [устойчивые функции](durable-functions-overview.md) для агрегаторов или в любых других ситуациях, когда нужен бесконечный цикл.

## <a name="orchestration-history"></a>Журнал оркестраций

As explained in the [orchestration history](durable-functions-orchestrations.md#orchestration-history) topic, the Durable Task Framework keeps track of the history of each function orchestration. Этот журнал постоянно растет, пока функция оркестрации продолжает планировать новые работы. Если функция оркестрации уходит в бесконечный цикл и продолжает планировать работы, этот журнал может достигнуть критического размера, что приведет к существенным проблемам с производительностью. Для устранения таких проблем в приложениях с бесконечными циклами была разработана концепция *нескончаемой оркестрации*.

## <a name="resetting-and-restarting"></a>Сброс и перезапуск

Instead of using infinite loops, orchestrator functions reset their state by calling the `ContinueAsNew` (.NET) or `continueAsNew` (JavaScript) method of the [orchestration trigger binding](durable-functions-bindings.md#orchestration-trigger). Этот метод принимает один параметр в формате JSON, который передает входные данные в следующие поколение функции оркестрации.

При вызове `ContinueAsNew` экземпляр помещает в очередь сообщение для себя и завершает выполнение. Это сообщение приводит к повторному запуску экземпляра с новыми входными значениями. Функция оркестратора сохраняет тот же идентификатор экземпляра, но журнал для нее обнуляется.

> [!NOTE]
> Для функции оркестрации, которая выполняет сброс с помощью `ContinueAsNew`, платформа устойчивых задач сохраняет идентификатор экземпляра, но создает новый внутренний *идентификатор выполнения*. Обычно этот идентификатор выполнения не передается наружу, но его можно использовать при отладке выполнения оркестрации.

## <a name="periodic-work-example"></a>Пример периодической работы

Нескончаемые оркестрации могут быть полезны, например, в коде, который должен неограниченно долго выполнять периодические работы.

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> The previous C# example is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript (только Функции 2.0)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

Разница между этим примером и запуском функции по таймеру заключается в том, что триггер очистки здесь можно не привязывать к расписанию. Например, если функция выполняется каждый час по расписанию CRON, она будет запущена в 1:00, 2:00, 3:00 и т.д., что может привести к проблемам наложения. А в нашем варианте, если очистка продолжается 30 минут, функция будет выполняться в 1:00, 2:30, 4:00, т. д., что позволяет избежать наложения.

## <a name="starting-an-eternal-orchestration"></a>Starting an eternal orchestration

Use the `StartNewAsync` (.NET) or the `startNew` (JavaScript) method to start an eternal orchestration, just like you would any other orchestration function.  

> [!NOTE]
> If you need to ensure a singleton eternal orchestration is running, it's important to maintain the same instance `id` when starting the orchestration. Дополнительные сведения см. в [статье об управлении экземплярами](durable-functions-instance-management.md).

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

## <a name="exit-from-an-eternal-orchestration"></a>Выход из нескончаемой оркестрации

Если потребуется завершить функцию оркестрации, достаточно лишь *не* вызывать метод `ContinueAsNew` и дождаться обычного выхода из функции.

If an orchestrator function is in an infinite loop and needs to be stopped, use the `TerminateAsync` (.NET) or `terminate` (JavaScript) method of the [orchestration client binding](durable-functions-bindings.md#orchestration-client) to stop it. Дополнительные сведения см. в [статье об управлении экземплярами](durable-functions-instance-management.md).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Узнайте, как реализовать одноэкземплярные оркестрации](durable-functions-singletons.md)
