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

### <a name="changing-activity-or-entity-function-signatures"></a>Изменение сигнатуры действия или функции сущности

Изменение сигнатуры относится к изменению имени, входных или выходных данных функции. Если этот тип изменений выполняется для действия или функции сущности, он может нарушить работу любой функции Orchestrator, которая зависит от нее. Обновление функции оркестратора для применения этого изменения может привести к нарушению работы имеющихся активных экземпляров.

Например, предположим, что у нас есть следующая функция Orchestrator.

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
> В предыдущих C# примерах целевой устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext`. Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

Это изменение никак не сказывается на всех новых экземплярах функции оркестратора, но нарушает работу активных экземпляров. Например, рассмотрим случай, когда экземпляр оркестрации вызывает функцию с именем `Foo`, возвращает логическое значение, а затем создает контрольные точки. Если на этой точке развернуто изменение сигнатуры, экземпляр с контрольной точкой завершится ошибкой сразу после возобновления работы и воспроизведения вызова `context.CallActivityAsync<int>("Foo")`. Этот сбой происходит из-за того, что результат в таблице журнала `bool`, но новый код пытается десериализовать его в `int`.

Этот пример является одним из множества различных способов, с помощью которых изменение сигнатуры может привести к нарушению работы существующих экземпляров. Как правило, изменение способа вызова функции для оркестратора может привести к проблемам.

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
> В предыдущих C# примерах целевой устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext`. Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

Это изменение добавит новый вызов функции в **SendNotification** между **Foo** и **Bar**. Изменения сигнатуры отсутствуют. Проблема возникает, когда имеющийся экземпляр возобновляет работу после вызова **Bar**. Во время воспроизведения, если исходный вызов **foo** вернул `true`, то в ходе воспроизведения Orchestrator будет вызываться **SendNotification**, который не находится в журнале выполнения. В результате платформа устойчивых задач завершается сбоем с `NonDeterministicOrchestrationException` из-за выполнения вызова **SendNotification** вместо **Bar**. Проблема такого же типа может возникнуть при добавлении любых вызовов "устойчивых" API-интерфейсов, включая `CreateTimer`, `WaitForExternalEvent`и т. д.

## <a name="mitigation-strategies"></a>Стратегии устранения рисков

Ниже приведены некоторые стратегии для устранения проблем с управлением версиями:

* ничего не предпринимать;
* остановить все активные экземпляры;
* выполнить параллельное развертывание.

### <a name="do-nothing"></a>ничего не предпринимать;

Самая простая реакция на критическое изменение — позволить активным экземплярам оркестрации завершиться сбоем. Новые экземпляры успешно выполняют измененный код.

Неисправность такого рода зависит от важности ваших экземпляров в полете. Эта проблема не критична, если вы активно выполняете разработку и активные экземпляры не имеют значения. Однако вам придется работать с исключениями и ошибками в конвейере диагностики. Если вы хотите этого избежать, необходимо рассмотреть другие варианты управления версиями.

### <a name="stop-all-in-flight-instances"></a>остановить все активные экземпляры;

Другим вариантом является остановка всех активных экземпляров. Остановка всех экземпляров может быть выполнена путем очистки содержимого внутренних очередей **управления —** очереди и **рабочего элемента** . Экземпляры будут постоянно зависнуть там, где они находятся, но они не будут перегружать журналы с сообщениями об ошибках. Этот подход идеально подходит для быстрой разработки прототипов.

> [!WARNING]
> Со временем сведения об этих очередях могут изменяться, поэтому не рекомендуется использовать этот метод для рабочих нагрузок.

### <a name="side-by-side-deployments"></a>выполнить параллельное развертывание.

Параллельное развертывание с более старыми версиями — наиболее отказоустойчивый способ обеспечить безопасное развертывание критических изменений. Это можно сделать с помощью любого из следующих способов:

* Развертывайте все обновления как полностью новые функции, не выкрывая существующие функции. Это может быть непросто, так как методы новых версий функций должны быть обновлены и следовать тем же рекомендациям.
* Развертывание всех обновлений в качестве нового приложения-функции с помощью другой учетной записи хранения.
* Разверните новую копию приложения-функции с той же учетной записью хранения, но с обновленным именем `taskHub`. Рекомендуется использовать параллельные развертывания.

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

#### <a name="functions-20"></a>Функции 2,0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

Значение по умолчанию для Устойчивые функции v1. x — `DurableFunctionsHub`. Начиная с Устойчивые функции версии 2.0, имя центра задач по умолчанию совпадает с именем приложения-функции в Azure или `TestHubName` при выполнении вне Azure.

Все сущности в службе хранилища Azure именуются на основе значения конфигурации `hubName`. Задавая новое имя для центра задач, вы гарантируете, что для новой версии приложения будут созданы отдельные очереди и таблица журнала. Однако приложение-функция будет прекращать обработку событий для оркестрации или сущностей, созданных в предыдущем имени центра задач.

Мы советуем развертывать новую версию приложения-функции в новый [слот развертывания](../functions-deployment-slots.md). Слоты развертывания позволяют параллельно запускать несколько копий приложения-функции, при этом только один слот может быть активным *рабочим* слотом. Предоставить новую логику оркестрации для имеющейся инфраструктуры может быть так же просто, как заменить новую версию в рабочем слоте.

> [!NOTE]
> Эта стратегия оптимально подходит при использовании триггеров HTTP и веб-перехватчика для функций оркестратора. Для триггеров, отличных от HTTP, таких как очереди или концентраторы событий, определение триггера должно [быть производным от параметра приложения](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) , который обновляется как часть операции переключения.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Узнайте, как управлять проблемами с производительностью и масштабированием](durable-functions-perf-and-scale.md)
