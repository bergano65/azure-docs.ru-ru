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
ms.openlocfilehash: 1b056ce8afe86fcd6629aff23ac95acae02ed9ba
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299877"
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
> Объект `context` в JavaScript не представляет DurableOrchestrationContext, а [контекст функции в целом](../functions-reference-node.md#context-object). Получить доступ к методам оркестрации можно через свойство `df` объекта `context`.

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

Если вы используете Visual Studio, можно выполнить привязку к клиенту оркестрации с помощью атрибута [орчестратионклиентаттрибуте](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .net для устойчивые функции 1,0. Начиная с предварительной версии Устойчивые функции 2,0 можно выполнить привязку к клиенту оркестрации с помощью атрибута .NET `DurableClientAttribute`.

Если вы используете языки сценариев (например, файлы *CSX* или *JS* ) для разработки, триггер ОРКЕСТРАЦИИ определяется следующим объектом JSON в массиве `bindings` файла *Function. JSON*:

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

В функциях .NET вы обычно выполняете привязку к `DurableOrchestrationClient`, что обеспечивает полный доступ ко всем клиентским API, поддерживаемым устойчивыми функциями. Начиная с Устойчивые функции 2,0, вместо этого вы привязываетесь к интерфейсу `IDurableOrchestrationClient`. В JavaScript те же интерфейсы API предоставляются объектом, возвращенным из `getClient`. API-интерфейсы в клиентском объекте включают в себя:

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

При использовании средств Visual Studio для функций Azure триггер сущности настраивается с помощью атрибута .NET `EntityTriggerAttribute`.

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

Каждая функция сущности имеет тип параметра `IDurableEntityContext`, который имеет следующие члены:

* **Имя_объекта**: имя выполняемой в данный момент сущности.
* **EntityKey**— ключ выполняемой в данный момент сущности.
* **EntityId**: идентификатор выполняющейся в данный момент сущности.
* **Имя_операции**: имя текущей операции.
* **Хасстате**: существует ли сущность, то есть имеет какое-то состояние. 
* **State @ no__t-1TState > ()** : Возвращает текущее состояние сущности. Если он еще не существует, он создается и инициализируется в `default<TState>`. Параметр `TState` должен быть примитивным или сериализуемым типом JSON. 
* **State @ no__t-1TState > (инитфунктион)** : Возвращает текущее состояние сущности. Если он еще не существует, он создается путем вызова указанного параметра `initfunction`. Параметр `TState` должен быть примитивным или сериализуемым типом JSON. 
* **Setstate (ARG)** : создает или обновляет состояние сущности. Параметр `arg` должен быть сериализуемым объектом или примитивом JSON.
* **Делетестате ()** : удаляет состояние сущности. 
* **Ввод @ no__t-1TInput > ()** : получает входные данные для текущей операции. Параметр типа `TInput` должен быть примитивным или сериализуемым типом JSON.
* **Return (ARG)** : Возвращает значение для оркестрации, вызвавшего операцию. Параметр `arg` должен быть объектом-примитивом или сериализуемым в формат JSON.
* **Сигналентити (EntityId, Operation, input)** : отправляет одностороннее сообщение в сущность. Параметр `operation` должен быть строкой, отличной от NULL, а параметр `input` должен быть объектом-примитивом или сериализуемым в формат JSON.
* **Креатеневорчестратион (орчестраторфунктионнаме, input)** : запускает новое согласование. Параметр `input` должен быть объектом-примитивом или сериализуемым в формат JSON.

Доступ к объекту `IDurableEntityContext`, переданному в функцию сущности, можно получить с @no__t помощью свойства Async-1 асинхронного. Этот подход удобен при использовании модели программирования на основе классов.

### <a name="trigger-sample-function-based-syntax"></a>Пример триггера (синтаксис на основе функций)

Следующий код является примером простой сущности *счетчика* , реализованной в виде устойчивой функции. Эта функция определяет три операции: `add`, `reset` и `get`, каждый из которых работает с целочисленным состоянием.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

Дополнительные сведения о синтаксисе на основе функций и способах его использования см. в разделе [синтаксис на основе функций](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-class-based-syntax"></a>Пример триггера (синтаксис на основе класса)

Следующий пример представляет собой эквивалентную реализацию сущности `Counter` с помощью классов и методов.

```csharp
[JsonObject(MemberSerialization.OptIn)]
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

Состояние этой сущности — объект типа `Counter`, который содержит поле, в котором хранится текущее значение счетчика. Чтобы сохранить этот объект в хранилище, он сериализуется и десериализуется библиотекой [JSON.NET](https://www.newtonsoft.com/json) . 

Дополнительные сведения о синтаксисе на основе класса и его использовании см. в разделе [Определение классов сущностей](durable-functions-dotnet-entities.md#defining-entity-classes).

> [!NOTE]
> Метод точки входа функции `[FunctionName]` с атрибутом *должен* быть объявлен `static` при использовании классов сущностей. Нестатические методы точек входа могут привести к инициализации нескольких объектов и возможно других неопределенных поведений.

Классы сущностей имеют специальные механизмы для взаимодействия с привязками и внедрением зависимостей .NET. Дополнительные сведения см. в разделе [Конструирование сущностей](durable-functions-dotnet-entities.md#entity-construction).

## <a name="entity-client"></a>Клиент сущности

Привязка клиента сущности позволяет асинхронно активировать [функции сущностей](#entity-trigger). Эти функции иногда называют [клиентскими функциями](durable-functions-types-features-overview.md#client-functions).

Если вы используете Visual Studio, можно выполнить привязку к клиенту сущности с помощью атрибута .NET `DurableClientAttribute`.

> [!NOTE]
> @No__t-0 можно также использовать для привязки к [клиенту оркестрации](#orchestration-client).

Если вы используете языки сценариев (например, файлы *. CSX* или *. js* ) для разработки, триггер СУЩНОСТИ определяется следующим объектом JSON в `bindings` массиве файла *Function. JSON*:

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

В функциях .NET обычно выполняется привязка к `IDurableEntityClient`, которая предоставляет полный доступ ко всем клиентским API, поддерживаемым устойчивыми сущностями. Также можно выполнить привязку к интерфейсу `IDurableClient`, который обеспечивает доступ к API-интерфейсам клиента для сущностей и оркестрации. API-интерфейсы в клиентском объекте включают в себя:

* **Реадентитистатеасинк @ no__t-1T >** : считывает состояние сущности. Он возвращает ответ, указывающий, существует ли целевая сущность, и, если да, то что ее состояние —.
* **Сигналентитясинк**: отправляет одностороннее сообщение в сущность и ожидает его постановки в очередь.

Нет необходимости создавать целевую сущность перед отправкой сигнала. состояние сущности можно создать в функции сущности, которая обрабатывает сигнал.

> [!NOTE]
> Важно понимать, что "сигналы", отправленные клиентом, просто помещаются в очередь, чтобы обрабатываться асинхронно позже. В частности, `SignalEntityAsync` обычно возвращает, прежде чем сущность запустит операцию, и вернуть возвращаемое значение или наблюдать за исключениями невозможно. Если требуются более надежные гарантии (например, для рабочих процессов), следует использовать *функции Orchestrator* , которые могут ожидать завершения операций сущностей и могут обрабатывать возвращаемые значения и наблюдать за исключениями.

### <a name="example-client-signals-entity-directly"></a>Пример: Клиент сообщает о сущности напрямую

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

### <a name="example-client-signals-entity-via-interface"></a>Пример: клиент сигнализирует о сущности через интерфейс

По возможности рекомендуется [обращаться к сущностям через интерфейсы](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) , так как она обеспечивает дополнительную проверку типов. Например, предположим, что упомянутая ранее сущность `Counter` реализовала интерфейс `ICounter`, определенный следующим образом:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

Затем клиентский код может использовать `SignalEntityAsync<ICounter>` для создания строго типизированного прокси-сервера:

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

Параметр `proxy` — это динамически создаваемый экземпляр `ICounter`, который внутренне преобразует вызов `Add` в эквивалентный (нетипизированный) вызов в `SignalEntityAsync`.

> [!NOTE]
> API `SignalEntityAsync` представляют односторонние операции. Если интерфейсы сущности возвращают `Task<T>`, значение параметра `T` всегда будет равно null или `default`.

В частности, не имеет смысла подать сигнал операции `Get`, так как значение не возвращается. Вместо этого клиенты могут использовать либо `ReadStateAsync` для прямого доступа к состоянию счетчика, либо запустить функцию Orchestrator, которая вызывает операцию `Get`. 

<a name="host-json"></a>
## <a name="hostjson-settings"></a>Параметры файла host.json

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Встроенная Справочник по HTTP API для управления экземплярами](durable-functions-http-api.md)
