---
title: Нескончаемые оркестрации в устойчивых функциях — Azure
description: Узнайте, как реализовать нескончаемые оркестрации с помощью расширения устойчивых функций для Функций Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d55e08fecbd1338284607ac59fe354c6fa8cb1ea
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478819"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Нескончаемые оркестрации в устойчивых функциях (Функции Azure)

*Нескончаемые оркестрации* — это функции оркестрации, которые никогда не останавливаются. Они позволяют создать [устойчивые функции](durable-functions-overview.md) для агрегаторов или в любых других ситуациях, когда нужен бесконечный цикл.

## <a name="orchestration-history"></a>Журнал оркестраций

Как поясняется в теме [истории оркестровки,](durable-functions-orchestrations.md#orchestration-history) система «Прочная задача» отслеживает историю оркестровки каждой функции. Этот журнал постоянно растет, пока функция оркестрации продолжает планировать новые работы. Если функция оркестрации уходит в бесконечный цикл и продолжает планировать работы, этот журнал может достигнуть критического размера, что приведет к существенным проблемам с производительностью. Для устранения таких проблем в приложениях с бесконечными циклами была разработана концепция *нескончаемой оркестрации*.

## <a name="resetting-and-restarting"></a>Сброс и перезапуск

Вместо того, чтобы использовать бесконечные циклы, функции `ContinueAsNew` оркестратора `continueAsNew` сбросить их состояние, позвонив (.NET) или (JavaScript) метод [акцептии триггера связывания.](durable-functions-bindings.md#orchestration-trigger) Этот метод принимает один параметр в формате JSON, который передает входные данные в следующие поколение функции оркестрации.

При вызове `ContinueAsNew` экземпляр помещает в очередь сообщение для себя и завершает выполнение. Это сообщение приводит к повторному запуску экземпляра с новыми входными значениями. Функция оркестратора сохраняет тот же идентификатор экземпляра, но журнал для нее обнуляется.

> [!NOTE]
> Для функции оркестрации, которая выполняет сброс с помощью `ContinueAsNew`, платформа устойчивых задач сохраняет идентификатор экземпляра, но создает новый внутренний *идентификатор выполнения*. Обычно этот идентификатор выполнения не передается наружу, но его можно использовать при отладке выполнения оркестрации.

## <a name="periodic-work-example"></a>Пример периодической работы

Нескончаемые оркестрации могут быть полезны, например, в коде, который должен неограниченно долго выполнять периодические работы.

# <a name="c"></a>[C #](#tab/csharp)

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
> Предыдущий пример сC —для прочных функций 2.x. Для долгосрочных функций 1.x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext`. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

---

Разница между этим примером и запуском функции по таймеру заключается в том, что триггер очистки здесь можно не привязывать к расписанию. Например, если функция выполняется каждый час по расписанию CRON, она будет запущена в 1:00, 2:00, 3:00 и т.д., что может привести к проблемам наложения. А в нашем варианте, если очистка продолжается 30 минут, функция будет выполняться в 1:00, 2:30, 4:00, т. д., что позволяет избежать наложения.

## <a name="starting-an-eternal-orchestration"></a>Начало вечной оркестровки

Используйте `StartNewAsync` метод (.NET) или `startNew` (JavaScript), чтобы начать вечную оркестровку, так же, как вы бы любой другой функции оркестровки.  

> [!NOTE]
> Если вам необходимо обеспечить вечную оркестровку синглтона, важно `id` поддерживать тот же экземпляр при запуске оркестровки. Дополнительные сведения см. в [статье об управлении экземплярами](durable-functions-instance-management.md).

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";

    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> Предыдущий код предназначен для функций durable 2.x. Для функций Durable 1.x `OrchestrationClient` необходимо использовать `DurableClient` атрибут вместо атрибута, а `IDurableOrchestrationClient`вместо этого — тип `DurableOrchestrationClient` параметра. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = "StaticId";
    
    // null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.startNew("Periodic_Cleanup_Loop", instanceId, null);

    context.log(`Started orchestration with ID = '${instanceId}'.`);
    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
};
```

---

## <a name="exit-from-an-eternal-orchestration"></a>Выход из нескончаемой оркестрации

Если потребуется завершить функцию оркестрации, достаточно лишь *не* вызывать метод `ContinueAsNew` и дождаться обычного выхода из функции.

Если функция оркестратора находится в бесконечном цикле и `TerminateAsync` нуждается в `terminate` остановке, используйте метод (.NET) или (JavaScript) [привязки клиента оркестровки,](durable-functions-bindings.md#orchestration-client) чтобы остановить его. Дополнительные сведения см. в [статье об управлении экземплярами](durable-functions-instance-management.md).

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Узнайте, как реализовать одноэкземплярные оркестрации](durable-functions-singletons.md)
