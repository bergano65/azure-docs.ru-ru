---
title: Привязки для устойчивых функций — Azure
description: Инструкции по использованию триггеров и привязок в расширении Устойчивых функций для Функций Azure.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 1f42c6c9b0086d49e539040334c83cfc0c6feb42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278224"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Привязки для устойчивых функций (Функции Azure)

Расширение [устойчивых функций](durable-functions-overview.md) содержит две новые привязки триггера, которые управляют выполнением функций действий и оркестратора. Он также содержит привязку к выходным данным, которая действует как клиент для среды выполнения устойчивых функций.

## <a name="orchestration-trigger"></a>Спусковой крючок оркестровки

Спусковой спусковой крючок позволяет автору [прочных функций оркестратора.](durable-functions-types-features-overview.md#orchestrator-functions) Этот триггер поддерживает запуск новых экземпляров функции оркестратора и возобновление существующих экземпляров функции оркестратора, которые "ожидают" задачу.

При использовании средств Visual Studio для Функций Azure триггер оркестрации настраивается с помощью атрибута .NET [OrchestrationTriggerAttribute](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttribute?view=azure-dotnet).

При написании функции оркестратора на языках сценариев (например,на JavaScript или C#) триггер оркестрации определяется следующим объектом JSON в массиве `bindings` файла *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`— это название оркестровки, которую клиенты должны использовать, когда они хотят начать новые экземпляры этой функции оркестратора. Это необязательное свойство. Если не указан, используется имя функции.

Внутри эта привязка триггера опрашивает серии очередей в учетной записи хранения по умолчанию приложения-функции. Эти очереди представляют собой сведения о внутренней реализации расширения, поэтому они не настраиваются явно в свойствах привязки.

### <a name="trigger-behavior"></a>Поведение триггера

Ниже приведены сведения о триггере оркестрации:

* **Однопоточная обработка**. Один поток диспетчера используется для выполнения всех функций оркестратора на одном экземпляре узла. По этой причине убедитесь, что код функции оркестратора эффективен и не выполняет никаких операций ввода-вывода. Также важно убедиться, что этот поток не выполняет никакой асинхронной работы, кроме тех случаев, когда ожидаются типы задач, специфичные для устойчивых функций.
* **Обработка сообщений о сбое.** В триггерах оркестрации не поддерживаются сообщения о сбое.
* **Видимость сообщений**. Сообщения триггеров оркестрации удаляются из очереди и сохраняются невидимыми в течение настраиваемого промежутка времени. Видимость этих сообщений обновляется автоматически, пока приложение-функция выполняется и работоспособно.
* **Возвращаемые значения**. Возвращаемые значения сериализуются в JSON и сохраняются в таблице с журналом оркестрации в хранилище таблиц Azure. Эти возвращаемые значения может запрашивать привязка клиента оркестрации, описанная ниже.

> [!WARNING]
> Функции оркестратора никогда не должны использовать никаких входных и выходных привязок, отличных от привязки триггера оркестрации. Это может вызвать проблемы с расширением устойчивых функций, так как эти привязки могут не подчиняться правилам однопоточности и ввода-вывода. Если вы хотите использовать другие привязки, добавьте их в функцию активности, вызванную функцией Orchestrator.

> [!WARNING]
> Функции оркестратора JavaScript никогда не должны быть объявлены как `async`.

### <a name="trigger-usage-net"></a>Использование триггера (.NET)

Привязка триггера оркестрации поддерживает входные и выходные данные. Ниже приведены некоторые сведения об обработке входных и выходных данных.

* **входы** - функции оркестровки .NET поддерживаются только `DurableOrchestrationContext` в качестве типа параметра. Входные данные десериализации непосредственно в сигнатуре функции не поддерживаются. Код должен `GetInput<T>` использовать метод (.NET) или `getInput` (JavaScript) для получения входов функции оркестратора. Эти входные данные должны быть типа, который сериализуется в JSON.
* **Выходные данные**. Триггеры оркестрации поддерживают выходные значения, а также входные данные. Возвращаемое значение функции используется для назначения выходного значения и должно быть типа, который сериализуется в JSON. Если функция .NET возвращает `Task` или `void`, значение `null` будет сохранено как выходное.

### <a name="trigger-sample"></a>Пример триггера

Следующий пример кода показывает, как может выглядеть простейшая функция оркестратора "Hello World":

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> Предыдущий код предназначен для функций durable 2.x. Для долгосрочных функций 1.x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext`. Для получения дополнительной информации о [Durable Functions Versions](durable-functions-versions.md) различиях между версиями см.

#### <a name="javascript-functions-20-only"></a>JavaScript (только Функции 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> Объект `context` в JavaScript не представляет собой durableOrchestrationContext, но [контекст функции в целом.](../functions-reference-node.md#context-object) Получить доступ к методам оркестрации можно через свойство `df` объекта `context`.

> [!NOTE]
> Для оркестраторов JavaScript нужно использовать `return`. Библиотека `durable-functions` отвечает за вызов метода `context.done`.

Большинство функций оркестратора вызывают функции действий. Пример "Hello World" демонстрирует, как вызвать функцию действия:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> Предыдущий код предназначен для функций durable 2.x. Для долгосрочных функций 1.x необходимо использовать `DurableOrchestrationContext` вместо `IDurableOrchestrationContext`. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

#### <a name="javascript-functions-20-only"></a>JavaScript (только Функции 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Триггер активности

Триггер активности позволяет автору функции, которые называются функциями оркестратора, известными как [функции активности.](durable-functions-types-features-overview.md#activity-functions)

Если вы используете Visual Studio, триггер активности `ActivityTriggerAttribute` настраивается с помощью атрибута .NET.

Если вы используете VS Code или портал Azure для разработки приложений, триггер действия определяется следующим объектом JSON в массиве `bindings` файла *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` является именем действия. Это значение — это имя, которое функции оркестратора используют для того, чтобы вызвать эту функцию действия. Это необязательное свойство. Если не указан, используется имя функции.

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

* **входы** - функции активности `DurableActivityContext` .NET, нативное использование в качестве типа параметра. Кроме того, функция действия может быть объявлена с любым типом параметра, который сериализуется в JSON. При использовании `DurableActivityContext`можно `GetInput<T>` вызвать вызов для получения и десеризмизации ввода функции активности.
* **Выходные данные.** Функции действий поддерживают выходные значения, а также входные данные. Возвращаемое значение функции используется для назначения выходного значения и должно быть типа, который сериализуется в JSON. Если функция .NET возвращает `Task` или `void`, значение `null` будет сохранено как выходное.
* **Метаданные**. Функции действий .NET можно привязать к параметру `string instanceId`, чтобы получить идентификатор экземпляра родительской оркестрации.

### <a name="trigger-sample"></a>Пример триггера

Следующий пример кода показывает, как может выглядеть простая функция действия "Hello World":

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> Предыдущий код предназначен для функций durable 2.x. Для долгосрочных функций 1.x необходимо использовать `DurableActivityContext` вместо `IDurableActivityContext`. Для получения дополнительной информации о [Durable Functions Versions](durable-functions-versions.md) различиях между версиями см.

Типом параметра по умолчанию для привязки `ActivityTriggerAttribute` .NET является `IDurableActivityContext`. Но триггеры действий .NET также поддерживают привязку непосредственно к JSON-сериализуемым типам (включая примитивные типы), поэтому эту же функцию можно упростить следующим образом:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (только Функции 2.0)

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

Можно использовать регулярные входные и выходные привязки в дополнение к связыванию триггера активности. Например, можно взять вход ный вход в привязку к активности и отправить сообщение на EventHub с помощью привязки вывода EventHub:

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

Связывание клиента оркестровки позволяет писать функции, которые взаимодействуют с функциями оркестратора. Эти функции иногда называют [функциями клиента.](durable-functions-types-features-overview.md#client-functions) Например, с экземплярами оркестрации можно взаимодействовать следующим образом:

* Запустите их.
* Запросите их состояние.
* Завершите их.
* Отправляйте им события во время их работы.
* Очистите журнал экземпляра.

Если вы используете Visual Studio, вы можете связаться `OrchestrationClientAttribute` с клиентом оркестровки, используя атрибут .NET для прочных функций 1.0. Начиная с функций durable 2.0, вы можете привязаться к клиенту оркестровки с помощью атрибута `DurableClientAttribute` .NET.

Если вы используете языки сценариев (например, *файлы .csx* или *.js)* для разработки, `bindings` триггер оркестровки определяется следующим объектом JSON в массиве *function.json*:

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

В функциях .NET обычно `IDurableOrchestrationClient`привязывается к , что дает вам полный доступ ко всем apIs-клиентам-клиентам, поддерживаемым Durable Functions. В старых выпусках Durable Functions 2.x `DurableOrchestrationClient` вместо этого вы связываетесь с классом. В JavaScript те же AA подвергаются `getClient`воздействию возвращаемого объекта. API-интерфейсы в клиентском объекте включают в себя:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Кроме того, функции .NET `T` `StartOrchestrationArgs` могут `JObject`связываться `IAsyncCollector<T>` с тем, где находится или .

Для получения дополнительной информации `IDurableOrchestrationClient` об этих операциях см.

### <a name="client-sample-visual-studio-development"></a>Пример клиента (разработка в Visual Studio)

Ниже приведен пример функции, активируемой с помощью очереди, которая запускает оркестрацию HelloWorld.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Предыдущий код C-кода предназначен для функций длительного пользования 2.x. Для функций Durable 1.x `OrchestrationClient` необходимо использовать `DurableClient` атрибут вместо атрибута, а `IDurableOrchestrationClient`вместо этого — тип `DurableOrchestrationClient` параметра. Для получения дополнительной информации о [Durable Functions Versions](durable-functions-versions.md) различиях между версиями см.

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
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> Предыдущий JSON предназначен для долгосрочных функций 2.x. Для функций durable 1.x `orchestrationClient` необходимо `durableClient` использовать вместо типа триггера. Для получения дополнительной информации о [Durable Functions Versions](durable-functions-versions.md) различиях между версиями см.

Ниже приведены примеры конкретно для языка, которые запускают новые экземпляры функции оркестратора.

#### <a name="c-script-sample"></a>Образец сценария СИ

В следующем примере показано, как использовать прочный связывание клиента оркестровки для запуска нового экземпляра функции функции СК, вызванной очередью:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Предыдущий код предназначен для функций durable 2.x. Для функций durable 1.x `DurableOrchestrationClient` необходимо использовать `IDurableOrchestrationClient`тип параметра вместо . Для получения дополнительной информации о [Durable Functions Versions](durable-functions-versions.md) различиях между версиями см.

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

## <a name="entity-trigger"></a>Спусковой крючок сущност

Триггеры сущности позволяют автору [функции сущности.](durable-functions-entities.md) Этот триггер поддерживает обработку событий для определенного экземпляра сущности.

При использовании инструментов Visual Studio для функций Azure `EntityTriggerAttribute` функциями триггер сущности настраивается с помощью атрибута .NET.

> [!NOTE]
> Спуски к образованию доступны начиная с функций Durable 2.x.

Внутри эта привязка триггера опрашивает серии очередей в учетной записи хранения по умолчанию приложения-функции. Эти очереди представляют собой сведения о внутренней реализации расширения, поэтому они не настраиваются явно в свойствах привязки.

### <a name="trigger-behavior"></a>Поведение триггера

Вот некоторые примечания о триггере сущности:

* **Однопотенный**: Для обработки операций для конкретной сущности используется один поток диспетчера. Если несколько сообщений отправляются в одну сущность одновременно, операции будут обрабатываться один за раз.
* **Обработка яда-сообщения** - В триггерах сущности нет поддержки яда сообщения.
* **Видимость сообщений** - Сообщения триггера Entity разлагаются и остаются невидимыми для настраиваемой продолжительности. Видимость этих сообщений обновляется автоматически, пока приложение-функция выполняется и работоспособно.
* **Значения возврата** - Функции сущности не поддерживают значения возврата. Существуют определенные AAP, которые могут быть использованы для сохранения значений состояния или передачи значений обратно в оркестровки.

Любые изменения состояния, внесенные в объект во время его выполнения, будут автоматически сохраняться после завершения выполнения.

### <a name="trigger-usage-net"></a>Использование триггера (.NET)

Каждая функция сущности `IDurableEntityContext`имеет тип параметра, который имеет следующие члены:

* **EntityName**: имя в настоящее время исполнительной сущности.
* **EntityKey**: ключ в настоящее время исполнитель сущности.
* **EntityId**: идентификатор объекта, исполняющего в настоящее время.
* **OperationName**: название текущей операции.
* **HasState**: существует ли сущность, то есть имеет какое-то состояние. 
* **GetState\<TState>()**: получает текущее состояние объекта. Если он еще не существует, он создается и инициализирован ы `default<TState>`. Параметр `TState` должен быть примитивным или JSON-серийным типом. 
* **GetState\<TState> (функция)**: получает текущее состояние сущности. Если он еще не существует, он создается путем вызова предоставленного `initfunction` параметра. Параметр `TState` должен быть примитивным или JSON-серийным типом. 
* **SetState (arg)**: создает или обновляет состояние объекта. Параметр `arg` должен быть объектом JSON-serializeable или примитивным.
* **DeleteState ()**: удаляет состояние объекта. 
* **GetInput\<TInput>()**: получает вход для текущей операции. Параметр `TInput` типа должен быть примитивным или JSON-серийным типом.
* **Return(arg)**: возвращает значение в оркестровку, которая называется операцией. Параметр `arg` должен быть примитивным или JSON-серийным объектом.
* **SignalEntity (EntityId, scheduledTimeUtc, операция, ввод)**: отправляет одностороннее сообщение объекту. Параметр `operation` должен быть ненулевой строкой, факультативным `scheduledTimeUtc` должно быть время даты `input` UTC, при котором можно вызвать операцию, а параметр должен быть примитивным или JSON-серийным объектом.
* **CreateNewOrchestration (orchestratorFunctionName, ввод)**: запускает новую оркестровку. Параметр `input` должен быть примитивным или JSON-серийным объектом.

Объект, `IDurableEntityContext` передаваемый функции сущности, может быть доступен с помощью `Entity.Current` свойства async-местного. Такой подход удобен при использовании модели программирования на основе классов.

### <a name="trigger-sample-c-function-based-syntax"></a>Триггерный образец (синтаксис на основе функций на основе СЗ)

Приведенный ниже код является примером простой сущности *Counter*, реализованной в формате устойчивой функции. Эта функция определяет три операции: `add`, `reset` и `get`, каждая из которых изменяет целочисленное значение состояния.

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

Для получения дополнительной информации о синтаксисе на [Function-Based Syntax](durable-functions-dotnet-entities.md#function-based-syntax)основе функций и о том, как его использовать, см.

### <a name="trigger-sample-c-class-based-syntax"></a>Триггерный образец (синтаксис на основе класса СЗ)

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

Состояние этой сущности представлено объектом типа `Counter`, который содержит поле с текущим значением счетчика. Чтобы сохранить этот объект в хранилище, он сериализуется и десериализуется библиотекой [Json.NET](https://www.newtonsoft.com/json). 

Дополнительные сведения о синтаксисе на основе классов и его использовании см. в [этой статье](durable-functions-dotnet-entities.md#defining-entity-classes).

> [!NOTE]
> Метод точки входа функции `[FunctionName]` с атрибутом *должен* быть объявлен `static` при использовании классов сущностей. Нестатические методы точек входа могут привести к инициализации нескольких объектов и возможно других неопределенных поведений.

Классы сущности имеют специальные механизмы взаимодействия с привязками и инъекцией зависимости .NET. Для получения дополнительной информации [см.](durable-functions-dotnet-entities.md#entity-construction)

### <a name="trigger-sample-javascript"></a>Образец триггера (JavaScript)

Следующий код является примером простой сущности *Counter,* реализованной как прочная функция, написанная в JavaScript. Эта функция определяет три операции: `add`, `reset` и `get`, каждая из которых изменяет целочисленное значение состояния.

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> Устойчивые сущности доступны в JavaScript начиная с версии **1.3.0** пакета npm `durable-functions`.

## <a name="entity-client"></a>Сущность клиента

Связывание клиента сущности позволяет асинхронно запускать [функции сущности.](#entity-trigger) Эти функции иногда называют [функциями клиента.](durable-functions-types-features-overview.md#client-functions)

Если вы используете Visual Studio, вы можете связаться `DurableClientAttribute` с клиентом сущности с помощью атрибута .NET.

> [!NOTE]
> Также `[DurableClientAttribute]` может быть использован для привязки к [клиенту оркестровки.](#orchestration-client)

Если вы используете языки сценариев (например, *файлы .csx* или *.js)* для разработки, триггер сущности определяется следующим объектом JSON в массиве `bindings` *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` используется в ситуациях, когда несколько приложений-функций совместно используют одну и ту же учетную запись хранения, но должны быть изолированы друг от друга. Если значение не задано, по умолчанию используется значение из `host.json`. Это значение должно соответствовать значению, используемому функциями целевого объекта.
* `connectionName` — имя параметра приложения, содержащего строку подключения к учетной записи хранения. Учетная запись хранилища, представленная этой строкой подключения, должна быть той же, которая используется функциями целевого объекта. Если она не указана, для приложения-функции используется строка подключения к учетной записи хранения по умолчанию.

> [!NOTE]
> В большинстве случаев мы рекомендуем опустить дополнительные свойства и полагаться на поведение по умолчанию.

### <a name="entity-client-usage"></a>Использование клиента entity

В функциях .NET обычно `IDurableEntityClient`привязывается к , что дает вам полный доступ ко всем клиентским AIS, поддерживаемым durable Entities. Вы также можете `IDurableOrchestrationClient` привязаться к интерфейсу, который обеспечивает доступ к клиентским AIS как для сущностей, так и для оркестровок. API-интерфейсы в клиентском объекте включают в себя:

* **ReadEntityStateAsync\<T>: **читает состояние сущности. Он возвращает ответ, указывающий на существование целевой сущности, и если да, то каково ее состояние.
* **SignalEntityAsync**: отправляет одностороннее сообщение объекту и ждет, когда оно будет закреплено.
* **ListEntitiesAsync**: запросы для состояния нескольких сущностей. Сущности могут быть запрошены по *имени* и *последнему времени операции.*

Нет необходимости создавать объект ную нитки перед отправкой сигнала - состояние сущности может быть создано из функции сущности, которая обрабатывает сигнал.

> [!NOTE]
> Важно понимать, что "сигналы", отправленные от клиента, просто enqueued, которые будут обработаны асинхронно на более позднее время. В частности, `SignalEntityAsync` обычно возвращается до того, как объект даже начинает операцию, и невозможно вернуть значение возврата или наблюдать исключения. Если требуются более сильные гарантии (например, для рабочих процессов), следует использовать *функции оркестратора,* которые могут ждать завершения операций сущности, а также обрабатывать значения возврата и наблюдать исключения.

### <a name="example-client-signals-entity-directly---c"></a>Пример: клиент сигнализирует сущность непосредственно - C #

Вот пример функции, вызванной очередью, вызываемый сущностью "Counter".

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

### <a name="example-client-signals-entity-via-interface---c"></a>Пример: сущность сигналов клиента через интерфейс - C #

Там, где это возможно, мы рекомендуем [доступ к объектам через интерфейсы,](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) поскольку это обеспечивает более проверку типов. Например, предположим, что `Counter` `ICounter` упомянутая ранее сущность реализовала интерфейс, определяемый следующим образом:

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

Клиентский код `SignalEntityAsync<ICounter>` может использоваться для создания прокси-сервера, безопасного для типов:

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

Параметр `proxy` представляет собой динамически `ICounter`сгенерированный экземпляр, `Add` который внутренне переводит вызов в `SignalEntityAsync`эквивалентный (нетипированный) вызов.

> [!NOTE]
> `SignalEntityAsync` AA представляют односторонние операции. Если интерфейсы сущности `Task<T>`возвращаются, `T` значение параметра `default`всегда будет нулевым или .

В частности, нет смысла сигнализировать `Get` об операции, так как значение не возвращается. Вместо этого клиенты `ReadStateAsync` могут использовать либо для прямого доступа к состоянию `Get` счетчика, либо запускать функцию оркестратора, которая вызывает операцию.

### <a name="example-client-signals-entity---javascript"></a>Пример: сущность сигналов клиента - JavaScript

Вот пример функции, вызванной очередью, которая сигнализирует о сущности "Counter" в JavaScript.

**function.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
      },
      {
        "name": "starter",
        "type": "durableClient",
        "direction": "in"
      }
    ],
    "disabled": false
  }
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> Устойчивые сущности доступны в JavaScript начиная с версии **1.3.0** пакета npm `durable-functions`.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>Параметры файла host.json

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Встроенная ссылка HTTP API на управление например](durable-functions-http-api.md)
