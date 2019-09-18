---
title: Привязки для устойчивых функций — Azure
description: Инструкции по использованию триггеров и привязок в расширении Устойчивых функций для Функций Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: f297c89d2c3ba5692a44fab631c0d46c75f48692
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033579"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Привязки для устойчивых функций (Функции Azure)

Расширение [устойчивых функций](durable-functions-overview.md) содержит две новые привязки триггера, которые управляют выполнением функций действий и оркестратора. Он также содержит привязку к выходным данным, которая действует как клиент для среды выполнения устойчивых функций.

## <a name="orchestration-trigger"></a>Триггер оркестрации

Триггер оркестрации позволяет создавать [устойчивые функции Orchestrator](durable-functions-types-features-overview.md#orchestrator-functions). Этот триггер поддерживает запуск новых экземпляров функции оркестратора и возобновление существующих экземпляров функции оркестратора, которые "ожидают" задачу.

При использовании средств Visual Studio для Функций Azure триггер оркестрации настраивается с помощью атрибута .NET [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html).

При написании функции оркестратора на языках сценариев (например,на JavaScript или C#) триггер оркестрации определяется следующим объектом JSON в массиве `bindings` файла *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` является именем оркестрации. Это значение, которое клиенты должны использовать, когда требуется запускать новые экземпляры этой функции оркестрации. Это необязательное свойство. Если не указан, используется имя функции.

Внутри эта привязка триггера опрашивает серии очередей в учетной записи хранения по умолчанию приложения-функции. Эти очереди представляют собой сведения о внутренней реализации расширения, поэтому они не настраиваются явно в свойствах привязки.

### <a name="trigger-behavior"></a>Поведение триггера

Ниже приведены сведения о триггере оркестрации:

* **Однопоточная обработка**. Один поток диспетчера используется для выполнения всех функций оркестратора на одном экземпляре узла. По этой причине убедитесь, что код функции оркестратора эффективен и не выполняет никаких операций ввода-вывода. Также важно убедиться, что этот поток не выполняет никакой асинхронной работы, кроме тех случаев, когда ожидаются типы задач, специфичные для устойчивых функций.
* **Обработка сообщений о сбое.** В триггерах оркестрации не поддерживаются сообщения о сбое.
* **Видимость сообщений**. Сообщения триггеров оркестрации удаляются из очереди и сохраняются невидимыми в течение настраиваемого промежутка времени. Видимость этих сообщений обновляется автоматически, пока приложение-функция выполняется и работоспособно.
* **Возвращаемые значения**. Возвращаемые значения сериализуются в JSON и сохраняются в таблице с журналом оркестрации в хранилище таблиц Azure. Эти возвращаемые значения может запрашивать привязка клиента оркестрации, описанная ниже.

> [!WARNING]
> Функции оркестратора никогда не должны использовать никаких входных и выходных привязок, отличных от привязки триггера оркестрации. Это может вызвать проблемы с расширением устойчивых функций, так как эти привязки могут не подчиняться правилам однопоточности и ввода-вывода. Если вы хотите использовать другие привязки, добавьте их в функцию действия, вызываемую из функции Orchestrator.

> [!WARNING]
> Функции оркестратора JavaScript никогда не должны быть объявлены как `async`.

### <a name="trigger-usage-net"></a>Использование триггера (.NET)

Привязка триггера оркестрации поддерживает входные и выходные данные. Ниже приведены некоторые сведения об обработке входных и выходных данных.

* **Входные данные**. Функции оркестрации .NET поддерживают только [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) в качестве типа параметра. Входные данные десериализации непосредственно в сигнатуре функции не поддерживаются. В коде необходимо использовать метод [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.NET) или `getInput` (JavaScript) для извлечения входных данных функции оркестратора. Эти входные данные должны быть типа, который сериализуется в JSON.
* **Выходные данные**. Триггеры оркестрации поддерживают выходные значения, а также входные данные. Возвращаемое значение функции используется для назначения выходного значения и должно быть типа, который сериализуется в JSON. Если функция .NET возвращает `Task` или `void`, значение `null` будет сохранено как выходное.

### <a name="trigger-sample"></a>Пример триггера

В следующем примере кода показано, как может выглядеть самая простая функция Orchestrator "Hello World":

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> Объект в JavaScript не представляет DurableOrchestrationContext, а [контекст функции в целом.](../functions-reference-node.md#context-object) `context` Получить доступ к методам оркестрации можно через свойство `df` объекта `context`.

> [!NOTE]
> Для оркестраторов JavaScript нужно использовать `return`. Библиотека `durable-functions` отвечает за вызов метода `context.done`.

Большинство функций оркестратора вызывают функции действий. Пример "Hello World" демонстрирует, как вызвать функцию действия:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Триггер действия

Триггер действия позволяет создавать функции, которые вызываются функциями Orchestrator, называемыми [функциями действий](durable-functions-types-features-overview.md#activity-functions).

Если вы используете Visual Studio, триггер действия настраивается с помощью атрибута .NET [ActvityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html).

Если вы используете VS Code или портал Azure для разработки приложений, триггер действия определяется следующим объектом JSON в массиве `bindings` файла *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` является именем действия. Это значение представляет собой имя, которое функции Orchestrator используют для вызова этой функции действия. Это необязательное свойство. Если не указан, используется имя функции.

Внутри эта привязка триггера опрашивает очередь в учетной записи хранения по умолчанию приложения-функции. Эта очередь представляет собой сведения о внутренней реализации расширения, поэтому она не настраивается явно в свойствах привязки.

### <a name="trigger-behavior"></a>Поведение триггера

Ниже приведены сведения о триггере действия:

* **Работа с потоками**. В отличие от триггера оркестрации, триггеры действий не имеют каких-либо ограничений при работе с потоками или операциями ввода-вывода. Они могут рассматриваться как обычные функции.
* **Обработка сообщений о сбое**. В триггерах действий не поддерживаются сообщения о сбое.
* **Видимость сообщений**. Сообщения триггеров действий удаляются из очереди и сохраняются невидимыми в течение настраиваемого промежутка времени. Видимость этих сообщений обновляется автоматически, пока приложение-функция выполняется и работоспособно.
* **Возвращаемые значения**. Возвращаемые значения сериализуются в JSON и сохраняются в таблице с журналом оркестрации в хранилище таблиц Azure.

> [!WARNING]
> Внутренняя часть хранилища функций действий является частью реализации, и код пользователя не должен взаимодействовать с этими сущностями хранилища напрямую.

### <a name="trigger-usage-net"></a>Использование триггера (.NET)

Привязка триггера действий поддерживает входные и выходные данные, как и триггер оркестрации. Ниже приведены некоторые сведения об обработке входных и выходных данных.

* **Входные данные**. Функции действий .NET изначально используют [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) как тип параметра. Кроме того, функция действия может быть объявлена с любым типом параметра, который сериализуется в JSON. При использовании `DurableActivityContext` можно вызвать [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) для извлечения и десериализации входных данных функции действия.
* **Выходные данные.** Функции действий поддерживают выходные значения, а также входные данные. Возвращаемое значение функции используется для назначения выходного значения и должно быть типа, который сериализуется в JSON. Если функция .NET возвращает `Task` или `void`, значение `null` будет сохранено как выходное.
* **Метаданные**. Функции действий .NET можно привязать к параметру `string instanceId`, чтобы получить идентификатор экземпляра родительской оркестрации.

### <a name="trigger-sample"></a>Пример триггера

В следующем примере кода показано, как может выглядеть простая функция действия "Hello World":

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Типом параметра по умолчанию для привязки `ActivityTriggerAttribute` .NET является `DurableActivityContext`. Но триггеры действий .NET также поддерживают привязку непосредственно к JSON-сериализуемым типам (включая примитивные типы), поэтому эту же функцию можно упростить следующим образом:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Привязки JavaScript также можно передать в качестве дополнительных параметров, поэтому одну и ту же функцию можно упростить следующим образом:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Использование входных и выходных привязок

В дополнение к привязке триггера действия можно использовать обычные входные и выходные привязки. Например, можно принять входные данные для привязки действия и отправить сообщение EventHub с помощью выходной привязки EventHub:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Клиент оркестрации

Привязка клиента оркестрации позволяет создавать функции, взаимодействующие с функциями Orchestrator. Эти функции иногда называют [клиентскими функциями](durable-functions-types-features-overview.md#client-functions). Например, с экземплярами оркестрации можно взаимодействовать следующим образом:

* Запустите их.
* Запросите их состояние.
* Завершите их.
* Отправляйте им события во время их работы.
* Очистите журнал экземпляра.

Если вы используете Visual Studio, можно выполнить привязку к клиенту оркестрации с помощью атрибута [орчестратионклиентаттрибуте](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .net для устойчивые функции 1,0. Начиная с предварительной версии устойчивые функции 2,0 можно выполнить привязку к клиенту оркестрации с помощью `DurableClientAttribute` атрибута .NET.

Если вы используете языки сценариев (например, файлы *. CSX* или *. js* ) для разработки, триггер оркестрации определяется следующим `bindings` объектом JSON в массиве файла *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` используется в ситуациях, когда несколько приложений-функций совместно используют одну и ту же учетную запись хранения, но должны быть изолированы друг от друга. Если значение не задано, по умолчанию используется значение из `host.json`. Оно должно соответствовать значению, которое используется целевыми функциями оркестратора.
* `connectionName` — имя параметра приложения, содержащего строку подключения к учетной записи хранения. Учетная запись хранения, представленная этой строкой подключения, должна быть той же, которая используется целевыми функциями оркестратора. Если она не указана, для приложения-функции используется строка подключения к учетной записи хранения по умолчанию.

> [!NOTE]
> В большинстве случаев рекомендуется исключить эти свойства и полагаться на поведение по умолчанию.

### <a name="client-usage"></a>Использование клиента

В функциях .NET вы обычно выполняете привязку к `DurableOrchestrationClient`, что обеспечивает полный доступ ко всем клиентским API, поддерживаемым устойчивыми функциями. Начиная с устойчивые функции 2,0, вместо этого выполняется привязка к `IDurableOrchestrationClient` интерфейсу. В JavaScript одни и те же API предоставляются объектом, возвращаемым из `getClient`. API-интерфейсы в клиентском объекте включают в себя:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_)
* [креатечеккстатусреспонсе](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_)
* [креатехттпманажементпайлоад](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_)

Кроме того, функции .NET могут выполнить привязку к `IAsyncCollector<T>`, где `T` — [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) или `JObject`.

Дополнительные сведения об этих операциях см. в документации по API [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) .

### <a name="client-sample-visual-studio-development"></a>Пример клиента (разработка в Visual Studio)

Ниже приведен пример функции, активируемой с помощью очереди, которая запускает оркестрацию HelloWorld.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>Пример клиента (не Visual Studio)

Если для разработки вы не используете Visual Studio, можно создать указанный ниже файл *function.json*. В этом примере показано, как настроить функцию, активируемую с помощью очереди, которая использует привязку клиента устойчивой оркестрации:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

Ниже приведены примеры конкретно для языка, которые запускают новые экземпляры функции оркестратора.

#### <a name="c-sample"></a>Пример на языке C#

В следующем примере показано, как использовать привязку клиента устойчивой оркестрации для запуска нового экземпляра функции из функции скрипта C #:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>Пример на языке JavaScript

В следующем примере показано, как использовать привязку клиента устойчивой оркестрации для запуска нового экземпляра функции из функции JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Дополнительные сведения о запуске экземпляров см. в статье [Управление экземплярами в устойчивых функциях (Функции Azure)](durable-functions-instance-management.md).

## <a name="entity-trigger"></a>Триггер сущности

Триггеры сущностей позволяют создавать [функции сущностей](durable-functions-entities.md). Этот триггер поддерживает обработку событий для конкретного экземпляра сущности.

При использовании средств Visual Studio для функций Azure триггер сущности настраивается с помощью `EntityTriggerAttribute` атрибута .NET.

> [!NOTE]
> Триггеры сущностей доступны в Устойчивые функции 2,0 и более поздних версий. Триггеры сущностей пока недоступны для JavaScript.

Внутри эта привязка триггера опрашивает серии очередей в учетной записи хранения по умолчанию приложения-функции. Эти очереди представляют собой сведения о внутренней реализации расширения, поэтому они не настраиваются явно в свойствах привязки.

### <a name="trigger-behavior"></a>Поведение триггера

Ниже приведены некоторые замечания о триггере сущности.

* **С одним потоком**: Для обработки операций с определенной сущностью используется один поток Dispatcher. Если несколько сообщений отправляются в одну сущность одновременно, операции будут обрабатываться один за раз.
* **Обработка подозрительных сообщений** — в триггерах сущностей нет поддержки подозрительных сообщений.
* **Видимость сообщений** — сообщения триггера сущности удаляются из очереди и остаются невидимыми в течение настраиваемого периода времени. Видимость этих сообщений обновляется автоматически, пока приложение-функция выполняется и работоспособно.
* **Возвращаемые значения** — функции сущностей не поддерживают возвращаемые значения. Существуют определенные API, которые можно использовать для сохранения состояния или передачи значений обратно в оркестрации.

Любые изменения состояния, внесенные в сущность во время ее выполнения, будут автоматически сохраняться после завершения выполнения.

### <a name="trigger-usage-net"></a>Использование триггера (.NET)

Каждая функция сущности имеет тип `IDurableEntityContext`параметра, который имеет следующие члены:

* **Имя_объекта**: Возвращает имя выполняющейся в данный момент сущности.
* Ключ **EntityKey**: Возвращает ключ выполняемой в данный момент сущности.
* **EntityId**: Возвращает идентификатор выполняющейся в данный момент сущности.
* **Имя_операции**: Возвращает имя текущей операции.
* **Исневликонструктед**: возвращает `true` , если сущность не существовала перед операцией.
* **TState-\<состояния > ()** : Возвращает текущее состояние сущности. `TState` Параметр должен быть примитивным или сериализуемым типом JSON.
* **Setstate (объект)** : обновляет состояние сущности. `object` Параметр должен быть примитивным или сериализуемым объектом JSON.
* **Тинпут ввода\<-> ()** : получает входные данные для текущей операции. Параметр `TInput` типа должен представлять тип, являющийся примитивом или сериализуемым в формат JSON.
* **Return (Object)** : Возвращает значение для оркестрации, вызвавшего операцию. `object` Параметр должен быть примитивным или сериализуемым объектом JSON.
* **Деструктонексит ()** : Удаляет сущность после завершения текущей операции.
* **Сигналентити (EntityId, строка, объект)** : отправляет одностороннее сообщение в сущность. `object` Параметр должен быть примитивным или сериализуемым объектом JSON.

При использовании режима `IDurableEntityContext` программирования сущностей на основе классов на объект можно ссылаться `Entity.Current` с помощью свойства потока-static.

### <a name="trigger-sample---entity-function"></a>Пример триггера — функция сущности

Следующий код является примером простой сущности счетчика, реализованной в виде стандартной функции. Эта функция определяет три *операции*, `add` `reset`, и `get`, `currentValue`каждая из которых работает с целочисленным значением состояния.

```csharp
[FunctionName(nameof(Counter))]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

### <a name="trigger-sample---entity-class"></a>Пример триггера — класс сущностей

Следующий пример является эквивалентной реализацией предыдущей `Counter` сущности с помощью классов и методов .NET.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

> [!NOTE]
> Метод точки входа функции с `[FunctionName]` атрибутом *должен* быть объявлен `static` при использовании классов сущностей. Нестатические методы точек входа могут привести к инициализации нескольких объектов и потенциально других неопределенных поведений.

Классы сущностей имеют специальные механизмы для взаимодействия с привязками и внедрением зависимостей .NET. Дополнительные сведения см. в статье о [устойчивых сущностях](durable-functions-entities.md) .

## <a name="entity-client"></a>Клиент сущности

Привязка клиента сущности позволяет асинхронно активировать [функции сущностей](#entity-trigger). Эти функции иногда называют [клиентскими функциями](durable-functions-types-features-overview.md#client-functions).

Если вы используете Visual Studio, можно выполнить привязку к клиенту сущности с помощью `DurableClientAttribute` атрибута .NET.

> [!NOTE]
> Также можно использовать для привязки к [клиенту оркестрации.](#orchestration-client) `[DurableClientAttribute]`

Если вы используете языки сценариев (например, файлы *. CSX* или *. js* ) для разработки, триггер сущности определяется следующим `bindings` объектом JSON в массиве файла *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub` используется в ситуациях, когда несколько приложений-функций совместно используют одну и ту же учетную запись хранения, но должны быть изолированы друг от друга. Если значение не задано, по умолчанию используется значение из `host.json`. Это значение должно соответствовать значению, используемому целевыми функциями сущности.
* `connectionName` — имя параметра приложения, содержащего строку подключения к учетной записи хранения. Учетная запись хранения, представленная этой строкой подключения, должна быть той же, которая используется целевыми функциями сущности. Если она не указана, для приложения-функции используется строка подключения к учетной записи хранения по умолчанию.

> [!NOTE]
> В большинстве случаев рекомендуется опустить необязательные свойства и полагаться на поведение по умолчанию.

### <a name="entity-client-usage"></a>Использование клиента сущности

В функциях .NET обычно выполняется привязка к `IDurableEntityClient`, которая предоставляет полный доступ ко всем клиентским API, поддерживаемым устойчивыми сущностями. Также можно выполнить привязку к `IDurableClient` интерфейсу, который предоставляет доступ к клиентским API как для сущностей, так и для оркестрации. API-интерфейсы в клиентском объекте включают в себя:

* **Реадентитистатеасинк\<T >** : считывает состояние сущности.
* **Сигналентитясинк**: отправляет одностороннее сообщение в сущность и ожидает его постановки в очередь.
* **Сигналентитясинк\<тентитинтерфаце >** : то же `SignalEntityAsync` , что использует созданный прокси-объект типа `TEntityInterface`.
* **Креатинтитипрокси\<тентитинтерфаце >** : динамически создает динамический прокси типа `TEntityInterface` для выполнения строго типизированных вызовов сущностей.

> [!NOTE]
> Важно понимать, что предыдущие операции с сигналами являются асинхронными. Невозможно вызвать функцию сущности и вернуть возвращаемое значение от клиента. Аналогично, `SignalEntityAsync` может возвращаться до того, как сущность начнет выполнение операции. Только функции Orchestrator могут вызывать функции сущностей синхронно и обрабатывать возвращаемые значения.

Для `SignalEntityAsync` API требуется указать уникальный идентификатор сущности в `EntityId`качестве. Эти API-интерфейсы также при необходимости принимают имя операции сущности как `string` и полезные данные операции в виде `object`сериализации JSON. Если целевая сущность не существует, она будет создана автоматически с указанным ИДЕНТИФИКАТОРом сущности.

### <a name="client-sample-untyped"></a>Пример клиента (нетипизированный)

Ниже приведен пример функции, активируемой в очереди, которая вызывает сущность "Counter".

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="client-sample-typed"></a>Пример клиента (типизированный)

Можно создать прокси-объект для строго типизированного доступа к операциям сущности. Для создания строго типизированного прокси-сервера тип сущности должен реализовать интерфейс. Например, предположим `Counter` , что упомянутая ранее сущность `ICounter` реализовала интерфейс, определенную следующим образом:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

Затем клиентский код может `SignalEntityAsync<TEntityInterface>` использовать и `ICounter` указать интерфейс в качестве параметра типа для создания строго типизированного прокси. Использование типобезопасных прокси-серверов показано в следующем примере кода:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

В предыдущем примере `proxy` параметр является динамически создаваемым `ICounter`экземпляром, который внутренне `Add` преобразует вызов в `SignalEntityAsync`эквивалентный (нетипизированный) вызов.

Существует несколько правил для определения интерфейсов сущности:

* Параметр `TEntityInterface` типа в `SignalEntityAsync<TEntityInterface>` должен быть интерфейсом.
* Интерфейсы сущности должны определять только методы.
* Методы интерфейса сущности не должны определять более одного параметра.
* Методы интерфейса сущности `void`должны возвращать `Task`, или `Task<T>` , `T` где — какое-либо возвращаемое значение.
* У интерфейсов сущностей должен быть ровно один конкретный класс реализации внутри одной и той же сборки (то есть класс сущностей).

Если любое из этих правил нарушается, `InvalidOperationException` во время выполнения будет создано исключение. В сообщении об исключении будет объяснено, какое правило было разорвано.

> [!NOTE]
> `SignalEntityAsync` API представляют односторонние операции. При возвращении `Task<T>`интерфейсов сущностей значение `T` параметра всегда будет равно null или `default`.

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Параметры файла host.json

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Встроенная Справочник по HTTP API для управления экземплярами](durable-functions-http-api.md)