---
title: Модульное тестирование устойчивых функций Azure
description: Информация о модульном тестировании устойчивых функций.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: glenga
ms.openlocfilehash: 0080365853e7a9c74d3ba0e5efb06ce5a3af2a21
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967103"
---
# <a name="durable-functions-unit-testing"></a>Модульное тестирование устойчивых функций

Модульное тестирование является важной частью современных способов разработки программного обеспечения. Модульные тесты позволяют проверить поведение бизнес-логики и предотвратить внедрение незамеченных критических изменений в будущем. Степень сложности устойчивых функций может быстро увеличиться, поэтому использование модульных тестов поможет предотвращать внесение критических изменений. В следующих разделах описывается модульное тестирование трех типов функций — клиента оркестрации, оркестратора и действий.

## <a name="prerequisites"></a>предварительные требования

Для выполнения примеров в этой статье нужно ознакомиться со следующими понятиями и платформами.

* Модульное тестирование

* Устойчивые функции

* [xUnit](https://xunit.github.io/) — платформа тестирования.

* [moq](https://github.com/moq/moq4) — платформа имитированной реализации.

## <a name="base-classes-for-mocking"></a>Базовые классы для имитации

Имитация поддерживается с помощью трех абстрактных классов Устойчивых функций:

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

* [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html);

Это базовые классы для [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html), [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) и [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), определяющих методы клиента оркестрации, оркестратора и действия. В процессе имитирования устанавливается ожидаемое поведение методов базового класса. Таким образом модульный тест может проверить бизнес-логику. При модульном тестировании бизнес-логики в клиенте оркестрации и оркестраторе выполняется двухэтапный рабочий процесс.

1. При определении сигнатур методов клиента оркестрации и оркестратора используйте базовые классы вместо конкретных реализаций.
2. Настройте модульные тесты, выполняющие имитирование поведения базовых классов и проверку бизнес-логики.

Подробные сведения по функциям тестирования, использующим привязку клиента оркестрации и триггера оркестратора, изложены в следующих разделах.

## <a name="unit-testing-trigger-functions"></a>Функции триггеров в модульном тестировании

В этом разделе модульный тест проверяет логику функции триггера HTTP, запускающей новые оркестрации.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Модульному тесту необходимо проверить заголовок `Retry-After` в полезных данных ответа. Поэтому модульный тест выполняет имитирование некоторых методов [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) для того, чтобы гарантировать предсказуемое поведение.

Во-первых, требуется имитирование базового класса [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Имитацией может выступить новый класс, реализующий [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Однако, использование платформы имитированной реализации наподобие [moq](https://github.com/moq/moq4) упрощает данный процесс.

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

Кроме того, модульный тест позволяет проверить формат выходных данных. В модульных тестах можно использовать типы параметров напрямую или имитировать класс [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html):

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Дополнительные сведения о xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Дополнительные сведения о moq](https://github.com/Moq/moq4/wiki/Quickstart)
