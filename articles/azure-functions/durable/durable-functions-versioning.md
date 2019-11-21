---
title: Управление версиями в устойчивых функциях — Azure
description: Сведения о том, как выполнять управление в расширении устойчивых функций для Функций Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232754"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Управление версиями в устойчивых функциях (Функции Azure)

Функции добавляются, удаляются и изменяются на протяжении времени существования приложения. [Устойчивые функции](durable-functions-overview.md) позволяют связывать функции ранее недоступными способами, и это влияет на управление версиями.

## <a name="how-to-handle-breaking-changes"></a>Как управлять критическими изменениями

Есть несколько критических изменений, о которых следует знать. В этой статье рассматриваются самые распространенные из них. Общим для них является то, что на новую и имеющуюся функции оркестрации влияют изменения кода функции.

### <a name="changing-activity-or-entity-function-signatures"></a>Changing activity or entity function signatures

Изменение сигнатуры относится к изменению имени, входных или выходных данных функции. If this kind of change is made to an activity or entity function, it could break any orchestrator function that depends on it. Обновление функции оркестратора для применения этого изменения может привести к нарушению работы имеющихся активных экземпляров.

As an example, suppose we have the following orchestrator function.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Эта упрощенная функция принимает результаты **Foo** и передает их в **Bar**. Предположим, что нужно изменить возвращаемое значение **Foo** с `bool` на `int` для поддержки более широкого набора итоговых значений. Результат должен выглядеть следующим образом:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> The previous C# examples target Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

Это изменение никак не сказывается на всех новых экземплярах функции оркестратора, но нарушает работу активных экземпляров. For example, consider the case where an orchestration instance calls a function named `Foo`, gets back a boolean value, and then checkpoints. Если на этой точке развернуто изменение сигнатуры, экземпляр с контрольной точкой завершится ошибкой сразу после возобновления работы и воспроизведения вызова `context.CallActivityAsync<int>("Foo")`. This failure happens because the result in the history table is `bool` but the new code tries to deserialize it into `int`.

This example is just one of many different ways that a signature change can break existing instances. Как правило, изменение способа вызова функции для оркестратора может привести к проблемам.

### <a name="changing-orchestrator-logic"></a>Изменение логики оркестратора

Другой класс проблем с управлением версиями связан с изменением кода функции оркестратора способом, который может сбить с толку логику воспроизведения для активных экземпляров.

Рассмотрим следующую функцию оркестратора:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Теперь предположим, что нужно внести небольшое изменение, чтобы добавить другой вызов функции.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> The previous C# examples target Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

Это изменение добавит новый вызов функции в **SendNotification** между **Foo** и **Bar**. Изменения сигнатуры отсутствуют. Проблема возникает, когда имеющийся экземпляр возобновляет работу после вызова **Bar**. During replay, if the original call to **Foo** returned `true`, then the orchestrator replay will call into **SendNotification**, which is not in its execution history. В результате платформа устойчивых задач завершается сбоем с `NonDeterministicOrchestrationException` из-за выполнения вызова **SendNotification** вместо **Bar**. The same type of problem can occur when adding any calls to "durable" APIs, including `CreateTimer`, `WaitForExternalEvent`, etc.

## <a name="mitigation-strategies"></a>Стратегии устранения рисков

Ниже приведены некоторые стратегии для устранения проблем с управлением версиями:

* Ничего не предпринимать
* Остановка всех активных экземпляров
* Выполнение параллельного развертывания

### <a name="do-nothing"></a>Ничего не предпринимать

Самая простая реакция на критическое изменение — позволить активным экземплярам оркестрации завершиться сбоем. Новые экземпляры успешно выполняют измененный код.

Whether this kind of failure is a problem depends on the importance of your in-flight instances. Эта проблема не критична, если вы активно выполняете разработку и активные экземпляры не имеют значения. However, you'll need to deal with exceptions and errors in your diagnostics pipeline. Если вы хотите этого избежать, необходимо рассмотреть другие варианты управления версиями.

### <a name="stop-all-in-flight-instances"></a>Остановка всех активных экземпляров

Другим вариантом является остановка всех активных экземпляров. Stopping all instances can be done by clearing the contents of the internal **control-queue** and **workitem-queue** queues. The instances will be forever stuck where they are, but they will not clutter your logs with failure messages. This approach is ideal in rapid prototype development.

> [!WARNING]
> Со временем сведения об этих очередях могут изменяться, поэтому не рекомендуется использовать этот метод для рабочих нагрузок.

### <a name="side-by-side-deployments"></a>Выполнение параллельного развертывания

Параллельное развертывание с более старыми версиями — наиболее отказоустойчивый способ обеспечить безопасное развертывание критических изменений. Это можно сделать с помощью любого из следующих способов:

* Deploy all the updates as entirely new functions, leaving existing functions as-is. This can be tricky because the callers of the new function versions must be updated as well following the same guidelines.
* Развертывание всех обновлений в качестве нового приложения-функции с помощью другой учетной записи хранения.
* Deploy a new copy of the function app with the same storage account but with an updated `taskHub` name. Side-by-side deployments is the recommended technique.

### <a name="how-to-change-task-hub-name"></a>Как изменить имя центра задач

Имя центра задач можно настроить в файле *host.json* следующим образом:

#### <a name="functions-1x"></a>Функции 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Functions 2.0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

The default value for Durable Functions v1.x is `DurableFunctionsHub`. Starting in Durable Functions v2.0, the default task hub name is the same as the function app name in Azure, or `TestHubName` if running outside of Azure.

Все сущности в службе хранилища Azure именуются на основе значения конфигурации `hubName`. Задавая новое имя для центра задач, вы гарантируете, что для новой версии приложения будут созданы отдельные очереди и таблица журнала. The function app, however, will stop processing events for orchestrations or entities created under the previous task hub name.

Мы советуем развертывать новую версию приложения-функции в новый [слот развертывания](../functions-deployment-slots.md). Слоты развертывания позволяют параллельно запускать несколько копий приложения-функции, при этом только один слот может быть активным *рабочим* слотом. Предоставить новую логику оркестрации для имеющейся инфраструктуры может быть так же просто, как заменить новую версию в рабочем слоте.

> [!NOTE]
> Эта стратегия оптимально подходит при использовании триггеров HTTP и веб-перехватчика для функций оркестратора. For non-HTTP triggers, such as queues or Event Hubs, the trigger definition should [derive from an app setting](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) that gets updated as part of the swap operation.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Узнайте, как управлять проблемами с производительностью и масштабированием](durable-functions-perf-and-scale.md)
