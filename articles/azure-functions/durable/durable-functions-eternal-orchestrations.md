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

Как описано в разделе [Журнал оркестрации](durable-functions-orchestrations.md#orchestration-history) , платформа устойчивых задач отслеживает историю каждого согласования функций. Этот журнал постоянно растет, пока функция оркестрации продолжает планировать новые работы. Если функция оркестрации уходит в бесконечный цикл и продолжает планировать работы, этот журнал может достигнуть критического размера, что приведет к существенным проблемам с производительностью. Для устранения таких проблем в приложениях с бесконечными циклами была разработана концепция *нескончаемой оркестрации*.

## <a name="resetting-and-restarting"></a>Сброс и перезапуск

Вместо бесконечных циклов функции Orchestrator сбрасывают свое состояние, вызывая метод `ContinueAsNew` (.NET) или `continueAsNew` (JavaScript) [привязки триггера оркестрации](durable-functions-bindings.md#orchestration-trigger). Этот метод принимает один параметр в формате JSON, который передает входные данные в следующие поколение функции оркестрации.

При вызове `ContinueAsNew` экземпляр помещает в очередь сообщение для себя и завершает выполнение. Это сообщение приводит к повторному запуску экземпляра с новыми входными значениями. Функция оркестратора сохраняет тот же идентификатор экземпляра, но журнал для нее обнуляется.

> [!NOTE]
> Для функции оркестрации, которая выполняет сброс с помощью *, платформа устойчивых задач сохраняет идентификатор экземпляра, но создает новый внутренний* идентификатор выполнения`ContinueAsNew`. Обычно этот идентификатор выполнения не передается наружу, но его можно использовать при отладке выполнения оркестрации.

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
> Предыдущий C# пример — для устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext`. Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

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

## <a name="starting-an-eternal-orchestration"></a>Запуск оркестрации нескончаемые

Используйте `StartNewAsync` (.NET) или метод `startNew` (JavaScript) для запуска оркестрации нескончаемые, как и любую другую функцию оркестрации.  

> [!NOTE]
> Если необходимо обеспечить выполнение оркестрации одноэлементного нескончаемые, важно поддерживать тот же экземпляр `id` при запуске оркестрации. Дополнительные сведения см. в [статье об управлении экземплярами](durable-functions-instance-management.md).

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
> Предыдущий код предназначен для Устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать атрибут `OrchestrationClient` вместо атрибута `DurableClient`, а вместо `IDurableOrchestrationClient`необходимо использовать тип параметра `DurableOrchestrationClient`. Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

## <a name="exit-from-an-eternal-orchestration"></a>Выход из нескончаемой оркестрации

Если потребуется завершить функцию оркестрации, достаточно лишь *не* вызывать метод `ContinueAsNew` и дождаться обычного выхода из функции.

Если функция Orchestrator находится в бесконечном цикле и должна быть остановлена, используйте метод `TerminateAsync` (.NET) или `terminate` (JavaScript) [привязки клиента orchestration](durable-functions-bindings.md#orchestration-client) , чтобы остановить его. Дополнительные сведения см. в [статье об управлении экземплярами](durable-functions-instance-management.md).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Узнайте, как реализовать одноэкземплярные оркестрации](durable-functions-singletons.md)
