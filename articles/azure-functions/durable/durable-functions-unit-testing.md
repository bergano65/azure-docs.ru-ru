---
title: Модульное тестирование устойчивых функций Azure
description: Информация о модульном тестировании устойчивых функций.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 86733f8b5b80799bad3e52c643ed27465dfc7641
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231226"
---
# <a name="durable-functions-unit-testing"></a>Модульное тестирование устойчивых функций

Модульное тестирование является важной частью современных способов разработки программного обеспечения. Модульные тесты позволяют проверить поведение бизнес-логики и предотвратить внедрение незамеченных критических изменений в будущем. Степень сложности устойчивых функций может быстро увеличиться, поэтому использование модульных тестов поможет предотвращать внесение критических изменений. The following sections explain how to unit test the three function types - Orchestration client, orchestrator, and activity functions.

> [!NOTE]
> This article provides guidance for unit testing for Durable Functions apps targeting Durable Functions 1.x. It has not yet been updated to account for changes introduced in Durable Functions 2.x. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

## <a name="prerequisites"></a>Технические условия

Для выполнения примеров в этой статье нужно ознакомиться со следующими понятиями и платформами.

* Модульное тестирование

* Устойчивые функции

* [xUnit](https://xunit.github.io/) — платформа тестирования.

* [moq](https://github.com/moq/moq4) — платформа имитированной реализации.

## <a name="base-classes-for-mocking"></a>Базовые классы для имитации

Mocking is supported via three abstract classes in Durable Functions 1.x:

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

These classes are base classes for `DurableOrchestrationClient`, `DurableOrchestrationContext`, and `DurableActivityContext` that define Orchestration Client, Orchestrator, and Activity methods. В процессе имитирования устанавливается ожидаемое поведение методов базового класса. Таким образом модульный тест может проверить бизнес-логику. При модульном тестировании бизнес-логики в клиенте оркестрации и оркестраторе выполняется двухэтапный рабочий процесс.

1. Use the base classes instead of the concrete implementation when defining orchestration client and orchestrator function signatures.
2. Настройте модульные тесты, выполняющие имитирование поведения базовых классов и проверку бизнес-логики.

Подробные сведения по функциям тестирования, использующим привязку клиента оркестрации и триггера оркестратора, изложены в следующих разделах.

## <a name="unit-testing-trigger-functions"></a>Функции триггеров в модульном тестировании

В этом разделе модульный тест проверяет логику функции триггера HTTP, запускающей новые оркестрации.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Модульному тесту необходимо проверить заголовок `Retry-After` в полезных данных ответа. So the unit test will mock some of `DurableOrchestrationClientBase` methods to ensure predictable behavior.

First, a mock of the base class is required, `DurableOrchestrationClientBase`. The mock can be a new class that implements `DurableOrchestrationClientBase`. Однако, использование платформы имитированной реализации наподобие [moq](https://github.com/moq/moq4) упрощает данный процесс.

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Затем метод `StartNewAsync` имитируется для возвращения идентификатора известного экземпляра.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Кроме того, `CreateCheckStatusResponse` имитируется таким образом, чтобы всегда возвращать пустой ответ HTTP 200.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

`ILogger` также имитируется.

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();
```  

Теперь `Run` метод вызывается из модульного теста.

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object,
        functionName,
        loggerMock.Object);
 ```

 На последнем шаге происходит сравнение вывода с ожидаемым значением.

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

После объединения всех шагов код модульного теста будет выглядеть следующим образом.

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Функции оркестратора для модульного тестирования

Функции оркестратора при модульном тестировании даже более интересны, так как в них обычно гораздо больше бизнес-логики.

В этом разделе модульные тесты проверяют выходные данные функции оркестратора `E1_HelloSequence`.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Код модульного теста начинается со строки для создания имитации.

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Затем имитируются вызовы метода действий.

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Далее модульный тест вызывает метод `HelloSequence.Run`.

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

И, наконец, проверяются выходные данные.

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

После объединения всех шагов код модульного теста будет выглядеть следующим образом.

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Функции действий модульного теста

Функции действий можно подвергать модульному тестированию таким же образом, как неустойчивые функции.

В этом разделе модульный тест проверяет поведение функции действия `E1_SayHello`.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Кроме того, модульный тест позволяет проверить формат выходных данных. The unit tests can use the parameter types directly or mock `DurableActivityContextBase` class:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Дополнительные сведения о xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Дополнительные сведения о moq](https://github.com/Moq/moq4/wiki/Quickstart)
