---
title: Управление экземплярами в устойчивых функциях (Azure)
description: Сведения о том, как управлять экземплярами в расширении устойчивых функций для Функций Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: ab9cc9b093008730d175fa3fde4391f9de236a84
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231378"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Управление экземплярами в Устойчивых функциях в Azure

Если вы используете расширение [устойчивые функции](durable-functions-overview.md) для функций Azure или хотите приступить к этому, убедитесь, что вы получаете оптимальное использование. Вы можете оптимизировать Устойчивые функции экземпляры оркестрации, изучите дополнительные сведения об управлении ими. В этой статье подробно описаны все операции по управлению экземплярами.

Можно запускать и завершать экземпляры, например, а также выполнять запросы к экземплярам, в том числе возможность запрашивать все экземпляры и экземпляры запросов с фильтрами. Кроме того, можно отправить события в экземпляры, дождаться завершения оркестрации и получить URL-адреса веб-перехватчиков управления HTTP. В этой статье также рассматриваются другие операции управления, включая сброс экземпляров, очистку журнала экземпляра и удаление центра задач.

В Устойчивые функции есть варианты реализации каждой из этих операций управления. В этой статье приведены примеры использования [Azure functions Core Tools](../functions-run-local.md) для .NET (C#) и JavaScript.

## <a name="start-instances"></a>Запуск экземпляров

Важно иметь возможность запустить экземпляр оркестрации. Обычно это делается при использовании привязки Устойчивые функции в триггере другой функции.

Метод `StartNewAsync` (.NET) или `startNew` (JavaScript) на [привязке клиента оркестрации](durable-functions-bindings.md#orchestration-client) запускает новый экземпляр. На внутреннем уровне этот метод помещает сообщение в очередь управления, что затем активирует начало функции с заданным именем, которое использует [привязку триггера оркестрации](durable-functions-bindings.md#orchestration-trigger).

Эта асинхронная операция завершается после успешного планирования процесса оркестрации.

Ниже приведены параметры для запуска нового экземпляра оркестрации.

* **Name.** Имя функции оркестратора, для которой назначается запуск.
* **Input.** Любые данные, сериализуемые в формат JSON, которые нужно передать в функцию оркестратора как входные данные.
* **InstanceId.** Уникальный идентификатор экземпляра (необязательно). Если этот параметр не указан, метод использует случайный идентификатор.

> [!TIP]
> Используйте случайный идентификатор экземпляра. Случайные идентификаторы экземпляров обеспечивают равномерное распределение нагрузки при масштабировании функций Orchestrator на нескольких виртуальных машинах. Правильное время на использование идентификаторов экземпляров, которые не являются случайными, заключается в том, что идентификатор должен поступать из внешнего источника или при реализации [одноэлементного шаблона Orchestrator](durable-functions-singletons.md) .

Следующий код является примером функции, запускающей новый экземпляр оркестрации:

### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> Предыдущий C# код предназначен для устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать атрибут `OrchestrationClient` вместо атрибута `DurableClient`, а вместо `IDurableOrchestrationClient`необходимо использовать тип параметра `DurableOrchestrationClient`. Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

### <a name="javascript"></a>JavaScript

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Кроме того, экземпляр можно запустить непосредственно с помощью команды [Azure Functions Core Tools](../functions-run-local.md) `durable start-new`. Она принимает следующие параметры.

* **`function-name` (обязательно)** : имя запускаемой функции.
* **`input` (необязательно)** : входные данные функции, встроенные или с помощью JSON-файла. Для файлов добавьте префикс в путь к файлу с `@`, например `@path/to/file.json`.
* **`id` (необязательный)** : идентификатор экземпляра оркестрации. Если этот параметр не указан, команда использует случайный идентификатор GUID.
* **`connection-string-setting` (необязательный)** : имя параметра приложения, содержащего нужную строку подключения к хранилищу. По умолчанию используется AzureWebJobsStorage.
* **`task-hub-name` (необязательно)** : имя используемого центра задач устойчивые функции. По умолчанию используется DurableFunctionsHub. Его также можно задать в [Host. JSON](durable-functions-bindings.md#host-json) с помощью DurableTask: HubName.

> [!NOTE]
> Команды основных средств предполагают, что вы используете их из корневого каталога приложения-функции. При явном предоставлении параметров `connection-string-setting` и `task-hub-name` можно выполнять команды из любого каталога. Несмотря на то, что эти команды можно выполнять без работающего узла приложения-функции, может оказаться, что вы не можете наблюдать некоторые последствия, если только узел не запущен. Например, команда `start-new` помещает в целевой центр задач сообщение о запуске, но согласование не выполняется, если не запущен процесс размещения приложения-функции, который может обработать сообщение.

Следующая команда запускает функцию HelloWorld и передает ей содержимое файла `counter-data.json`:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Экземпляры запросов

В рамках усилий по управлению взаимодействием, скорее всего, потребуется собрать сведения о состоянии экземпляра оркестрации (например, о том, завершился ли он нормально или неудачно).

Метод `GetStatusAsync` (.NET) или `getStatus` (JavaScript) в [привязке клиента оркестрации](durable-functions-bindings.md#orchestration-client) запрашивает состояние экземпляра оркестрации.

Он принимает параметры `instanceId` (обязательный), `showHistory` (необязательный), `showHistoryOutput` (необязательный) и `showInput` (необязательный).

* **`showHistory`** . Если задано значение `true`, ответ содержит журнал выполнения.
* **`showHistoryOutput`** : Если задано значение `true`, журнал выполнения содержит выходные данные действия.
* **`showInput`** . Если задано значение `false`, ответ не будет содержать входные данные функции. По умолчанию используется значение `true`.

Этот метод возвращает объект со следующими свойствами:

* **Name.** Имя функции оркестратора.
* **InstanceId.** Идентификатор экземпляра оркестрации (будет совпадать с переданным значением `instanceId`).
* **CreatedTime.** Время создания, то есть время начала выполнения функции оркестратора.
* **LastUpdatedTime.** Время прохождения последней контрольной точки оркестрации.
* **Input.** Входные данные функции в формате JSON. Это поле не заполняется, если `showInput` имеет значение false.
* **CustomStatus.** Настраиваемое значение состояния оркестрации в формате JSON.
* **Output.** Выходные данные функции в формате JSON (если выполнение функции успешно завершено). Если не удалось выполнить функцию Orchestrator, это свойство содержит сведения об ошибке. Если функция Orchestrator была прервана, это свойство включает причину завершения (если есть).
* **RuntimeStatus.** Состояние выполнения может иметь одно из следующих значений:
  * **Pending**. Выполнение экземпляра было запланировано, но еще не начато.
  * **Running.** Началось выполнение экземпляра.
  * **Completed.** Выполнение экземпляра завершилось в обычном режиме.
  * **ContinuedAsNew.** Экземпляр выполнил перезапуск с очисткой журнала выполнения. Это состояние является временным.
  * **Failed.** Выполнение экземпляра завершилось ошибкой.
  * **Terminated**: выполнение экземпляра было прервано.
* **History**: журнал выполнения оркестрации. Это поле заполняется, только если `showHistory` имеет значение `true`.

Этот метод возвращает `null` (.NET) или `undefined` (JavaScript), если экземпляр не существует.

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Предыдущий C# код предназначен для устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать атрибут `OrchestrationClient` вместо атрибута `DurableClient`, а вместо `IDurableOrchestrationClient`необходимо использовать тип параметра `DurableOrchestrationClient`. Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Также можно напрямую получить состояние экземпляра оркестрации с помощью команды [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status`. Она принимает следующие параметры.

* **`id` (обязательно)** : идентификатор экземпляра оркестрации.
* **`show-input` (необязательно)** . Если задано значение `true`, ответ содержит входные данные функции. По умолчанию используется значение `false`.
* **`show-output` (необязательно)** . Если задано значение `true`, ответ содержит выходные данные функции. По умолчанию используется значение `false`.
* **`connection-string-setting` (необязательный)** : имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательно)** : имя используемого центра задач устойчивые функции. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать в [Host. JSON](durable-functions-bindings.md#host-json)с помощью DurableTask: HubName.

Следующая команда извлекает состояние (включая входные и выходные данные) экземпляра с ИДЕНТИФИКАТОРом экземпляра оркестрации 0ab8c55a66644d68a3a8b220b12d209c. Предполагается, что вы выполняете команду `func` из корневого каталога приложения функции:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Для получения журнала экземпляра оркестрации можно использовать команду `durable get-history`. Она принимает следующие параметры.

* **`id` (обязательно)** : идентификатор экземпляра оркестрации.
* **`connection-string-setting` (необязательный)** : имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательно)** : имя используемого центра задач устойчивые функции. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать в Host. JSON с помощью durableTask: HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Запрос всех экземпляров

Вместо того, чтобы запрашивать один экземпляр в согласовании одновременно, может оказаться, что он более эффективен для выполнения запросов сразу.

С помощью метода `GetStatusAsync` (.NET) или `getStatusAll` (JavaScript) вы можете запросить данные о состоянии всех экземпляров оркестрации. В .NET можно передать объект `CancellationToken` на случай, если вы хотите отменить его. Метод возвращает объекты с теми же свойствами, что и метод `GetStatusAsync` с параметрами.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> Предыдущий C# код предназначен для устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать атрибут `OrchestrationClient` вместо атрибута `DurableClient`, а вместо `IDurableOrchestrationClient`необходимо использовать тип параметра `DurableOrchestrationClient`. Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Кроме того, можно напрямую запрашивать экземпляры с помощью команды [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances`. Она принимает следующие параметры.

* **`top` (необязательный)** : эта команда поддерживает разбиение на страницы. Этот параметр соответствует количеству экземпляров, получаемых по запросу. По умолчанию задано значение 10.
* **`continuation-token` (необязательно)** : токен, указывающий, какую страницу или раздел экземпляров нужно извлечь. Каждое выполнение `get-instances` возвращает маркер для извлечения следующего набора экземпляров.
* **`connection-string-setting` (необязательный)** : имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательно)** : имя используемого центра задач устойчивые функции. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать в [Host. JSON](durable-functions-bindings.md#host-json)с помощью DurableTask: HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Экземпляры запросов с фильтрами

Что делать, если вам не нужна вся информация, которую может предоставить запрос стандартного экземпляра? Например, что делать, если вы просто ищете время создания оркестрации или состояние среды выполнения оркестрации? Вы можете ограничивать запрос, применяя фильтры.

Используйте метод `GetStatusAsync` (.NET) или `getStatusBy` (JavaScript) для получения списка экземпляров оркестрации, соответствующих набору предопределенных фильтров.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> Предыдущий C# код предназначен для устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать атрибут `OrchestrationClient` вместо атрибута `DurableClient`, а вместо `IDurableOrchestrationClient`необходимо использовать тип параметра `DurableOrchestrationClient`. Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

В Azure Functions Core Tools можно также использовать команду `durable get-instances` с фильтрами. В дополнение к вышеупомянутым параметрам `top`, `continuation-token`, `connection-string-setting`и `task-hub-name` можно использовать три параметра фильтра (`created-after`, `created-before`и `runtime-status`).

* **`created-after` (необязательный)** : получение экземпляров, созданных после этой даты и времени в формате UTC. Принимаются значения даты и времени в формате ISO 8601.
* **`created-before` (необязательный)** : получение экземпляров, созданных до этой даты и времени в формате UTC. Принимаются значения даты и времени в формате ISO 8601.
* **`runtime-status` (необязательно)** : получение экземпляров с определенным состоянием (например, "выполняется" или "завершено"). Можно указать несколько состояний (через пробел).
* **`top` (необязательный)** : количество экземпляров, получаемых по запросу. По умолчанию задано значение 10.
* **`continuation-token` (необязательно)** : токен, указывающий, какую страницу или раздел экземпляров нужно извлечь. Каждое выполнение `get-instances` возвращает маркер для извлечения следующего набора экземпляров.
* **`connection-string-setting` (необязательный)** : имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательно)** : имя используемого центра задач устойчивые функции. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать в [Host. JSON](durable-functions-bindings.md#host-json)с помощью DurableTask: HubName.

Если не указать фильтры (`created-after`, `created-before`или `runtime-status`), команда просто извлекает экземпляры `top`, не обращаясь в состояние выполнения или время создания.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Завершить экземпляры

Если у вас есть экземпляр оркестрации, выполнение которого занимает слишком много времени, или необходимо остановить его до завершения по какой-либо причине, у вас есть возможность завершить его.

Для завершения экземпляров можно использовать метод `TerminateAsync` (.NET) или `terminate` (JavaScript) [привязки клиента оркестрации](durable-functions-bindings.md#orchestration-client) . Эти два параметра являются `instanceId` и `reason` строкой, которая записывается в журналы и в состояние экземпляра. Прерванный экземпляр прекращает работу сразу после достижения следующей `await` (.NET) или `yield` (JavaScript) или немедленно, если он уже находится в `await` или `yield`.

### <a name="c"></a>C#

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Предыдущий C# код предназначен для устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать атрибут `OrchestrationClient` вместо атрибута `DurableClient`, а вместо `IDurableOrchestrationClient`необходимо использовать тип параметра `DurableOrchestrationClient`. Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> Прекращение работы экземпляра в настоящее время не распространяется. Функции действий и взаимосвязи выполняются до завершения независимо от того, был ли завершен экземпляр оркестрации, вызвавший их.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Кроме того, экземпляр оркестрации можно завершить напрямую, используя команду [Azure Functions Core Tools](../functions-run-local.md) `durable terminate`. Она принимает следующие параметры.

* **`id` (обязательно)** : идентификатор экземпляра оркестрации для завершения.
* **`reason` (необязательно)** : Причина завершения.
* **`connection-string-setting` (необязательный)** : имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательно)** : имя используемого центра задач устойчивые функции. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать в [Host. JSON](durable-functions-bindings.md#host-json)с помощью DurableTask: HubName.

Следующая команда завершает экземпляр оркестрации с ИДЕНТИФИКАТОРом 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Отправка событий в экземпляры

В некоторых случаях очень важно, чтобы функции Orchestrator могли ожидать и прослушивать внешние события. Сюда входят [функции](durable-functions-overview.md#monitoring) и функции мониторинга, ожидающие [взаимодействия человека](durable-functions-overview.md#human).

Отправка уведомлений о событиях в выполняемые экземпляры с помощью метода `RaiseEventAsync` (.NET) или метода `raiseEvent` (JavaScript) [привязки клиента orchestration](durable-functions-bindings.md#orchestration-client). Экземпляры, которые могут выполнять эти события, — это те, которые ожидают вызова `WaitForExternalEvent` (.NET) или вызова метода `waitForExternalEvent` (JavaScript).

Ниже приведены параметры для `RaiseEventAsync` (.NET) и `raiseEvent` (JavaScript).

* **InstanceId** — уникальный идентификатор экземпляра;
* **EventName** — имя отправляемого события;
* **EventData** — данные полезной нагрузки в формате JSON, отправляемые в экземпляр.

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Предыдущий C# код предназначен для устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать атрибут `OrchestrationClient` вместо атрибута `DurableClient`, а вместо `IDurableOrchestrationClient`необходимо использовать тип параметра `DurableOrchestrationClient`. Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!NOTE]
> Если экземпляр оркестрации с указанным ИДЕНТИФИКАТОРом экземпляра отсутствует, сообщение о событии отбрасывается. Если экземпляр существует, но еще не ожидает событие, то событие будет храниться в состоянии экземпляра до тех пор, пока оно не будет готово к получению и обработке.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Событие можно также вызвать непосредственно в экземпляре оркестрации с помощью команды [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event`. Она принимает следующие параметры.

* **`id` (обязательно)** : идентификатор экземпляра оркестрации.
* **`event-name`** : имя события, которое необходимо вызвать.
* **`event-data` (необязательный)** : данные, отправляемые в экземпляр оркестрации. Это может быть путь к JSON-файлу или данные, которые можно указать непосредственно в командной строке.
* **`connection-string-setting` (необязательный)** : имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательно)** : имя используемого центра задач устойчивые функции. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать в [Host. JSON](durable-functions-bindings.md#host-json)с помощью DurableTask: HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Дождитесь, пока завершится оркестрация.

В долгосрочных согласованиях может потребоваться подождать и получить результаты оркестрации. В таких случаях также полезно иметь возможность определить период времени ожидания для оркестрации. При превышении времени ожидания возвращается состояние оркестрации, а не результаты.

Для синхронного получения фактических выходных данных из экземпляра оркестрации можно использовать `WaitForCompletionOrCreateCheckStatusResponseAsync` (.NET) или метод `waitForCompletionOrCreateCheckStatusResponse` (JavaScript). По умолчанию эти методы используют значение по умолчанию 10 секунд для `timeout`и 1 секунда для `retryInterval`.  

Ниже приведен пример функции HTTP-триггера, в котором показано, как использовать этот API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Вызовите функцию с помощью следующей строки. Для интервала повтора используйте 2 секунды в течение времени ожидания и 0,5 секунд.

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

В зависимости от того, сколько времени уйдет на получение ответа от экземпляра оркестрации, возможны два варианта развития событий.

* Экземпляры оркестрации завершены в течение заданного времени ожидания (в данном случае 2 секунды), а ответ является фактическим выходным экземпляром оркестрации, который доставляется синхронно:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:14:29 GMT
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* Экземпляры оркестрации не могут быть завершены в течение заданного времени ожидания, и ответ по умолчанию описан в разделе [обнаружение URL-адреса API HTTP](durable-functions-http-api.md):

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> В зависимости от используемой версии узла функций Azure может отличаться формат URL-адресов веб перехватчиков. Описанный выше пример предназначен для узла Функций Azure 2.0.

## <a name="retrieve-http-management-webhook-urls"></a>Получение URL-адресов веб-перехватчика управления HTTP

Внешнюю систему можно использовать для наблюдения или вызова событий в оркестрации. Внешние системы могут обмениваться данными с Устойчивые функции через URL-адреса перехватчика, которые являются частью ответа по умолчанию, описанного в разделе [обнаружение URL-адресов API HTTP](durable-functions-http-features.md#http-api-url-discovery). Веб-перехватчики можно также получить программно с помощью [привязки клиента оркестрации](durable-functions-bindings.md#orchestration-client). С помощью методов `CreateHttpManagementPayload` (.NET) или `createHttpManagementPayload` (JavaScript) можно получить сериализуемый объект, который содержит эти URL-адреса перехватчиков.

Методы `CreateHttpManagementPayload` (.NET) и `createHttpManagementPayload` (JavaScript) имеют один параметр:

* **InstanceId** — уникальный идентификатор экземпляра.

Методы возвращают объект со следующими строковыми свойствами:

* **Id** — идентификатор экземпляра оркестрации (будет совпадать с переданным значением `InstanceId`).
* **StatusQueryGetUri** — URL-адрес состояния экземпляра оркестрации.
* **SendEventPostUri** — URL-адрес вызова события экземпляра оркестрации.
* **TerminatePostUri** — URL-адрес завершения работы экземпляра оркестрации.
* **Пуржехисториделетеури**: URL-адрес "Очистка журнала" экземпляра оркестрации.

Функции могут отсылать экземпляры этих объектов во внешние системы для отслеживания или вызова событий в соответствующих оркестрации, как показано в следующих примерах:

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

> [!NOTE]
> Предыдущий C# код предназначен для устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать `DurableActivityContext` вместо `IDurableActivityContext`, необходимо использовать атрибут `OrchestrationClient` вместо атрибута `DurableClient`, а вместо `DurableOrchestrationClient` необходимо использовать тип параметра `IDurableOrchestrationClient`. Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

## <a name="rewind-instances-preview"></a>Перемотка экземпляров (Предварительная версия)

При возникновении сбоя оркестрации в случае непредвиденной причины можно *Перемотать* экземпляр на предыдущее работоспособное состояние, используя API, созданный для этой цели.

> [!NOTE]
> Этот API не является заменой правильной политики повторов и обработки ошибок. Вместо этого его следует применять исключительно в тех случаях, когда причини сбоя экземпляра оркестрации неизвестные. Дополнительные сведения об обработке ошибок и политиках повтора см. в статье [об обработке ошибок](durable-functions-error-handling.md) .

Используйте метод `RewindAsync` (.NET) или `rewind` (JavaScript) [привязки клиента оркестрации](durable-functions-bindings.md#orchestration-client) , чтобы перевести согласование обратно в состояние *выполнения* . Этот метод также приведет к повторному выполнению действий или сбоев при взаимодействии, которые привели к сбою оркестрации.

Например, предположим, что у вас есть рабочий процесс, включающий серию [утверждений человека](durable-functions-overview.md#human). Предположим, что существует ряд функций действий, которые уведомляют пользователей о необходимости их утверждения и ожидают ответа в режиме реального времени. После того как все действия по утверждению получили ответы или истекло время ожидания, предположим, что другое действие завершается сбоем из-за неправильной настройки приложения, например неверной строки подключения к базе данных. Как результат — сбой оркестрации в рабочем процессе. С помощью API `RewindAsync` (.NET) или `rewind` (JavaScript) Администратор приложения может исправить ошибку конфигурации и перемотать неудачное согласование обратно в состояние непосредственно перед сбоем. Ни один из действий человека не должен быть утвержден повторно, и теперь согласование можно успешно завершить.

> [!NOTE]
> Функция *перемотки назад* не поддерживает Перемотка экземпляров оркестрации, использующих устойчивые таймеры.

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> Предыдущий C# код предназначен для устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать атрибут `OrchestrationClient` вместо атрибута `DurableClient`, а вместо `IDurableOrchestrationClient`необходимо использовать тип параметра `DurableOrchestrationClient`. Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Можно также перемотать экземпляр оркестрации напрямую с помощью команды [Azure Functions Core Tools](../functions-run-local.md) `durable rewind`. Она принимает следующие параметры.

* **`id` (обязательно)** : идентификатор экземпляра оркестрации.
* **`reason` (необязательно)** : причина перемотки экземпляра оркестрации.
* **`connection-string-setting` (необязательный)** : имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательно)** : имя используемого центра задач устойчивые функции. По умолчанию используется имя центра задач в файле [Host. JSON](durable-functions-bindings.md#host-json) .

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Очистить журнал экземпляров

Чтобы удалить все данные, связанные с согласованием, можно очистить журнал экземпляров. Например, может потребоваться удалить все строки таблицы Azure и большие двоичные объекты сообщений, связанные с завершенным экземпляром. Для этого используйте метод `PurgeInstanceHistoryAsync` (.NET) или `purgeInstanceHistory` (JavaScript) [привязки клиента оркестрации](durable-functions-bindings.md#orchestration-client).

Этот метод имеет две перегрузки. Первая перегрузка удаляет журнал по ИДЕНТИФИКАТОРу экземпляра оркестрации:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

В следующем примере показана функция, активируемая с помощью таймера, которая очищает журнал для всех экземпляров оркестрации, завершенных по истечении указанного интервала времени. В этом случае он удаляет данные для всех экземпляров, выполненных 30 или более дней назад. Оно запускается раз в день, в 00:00:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
        DateTime.MinValue,
        DateTime.UtcNow.AddDays(-30),  
        new List<OrchestrationStatus>
        {
            OrchestrationStatus.Completed
        });
}
```

> [!NOTE]
> Предыдущий C# код предназначен для устойчивые функции 2. x. Для Устойчивые функции 1. x необходимо использовать атрибут `OrchestrationClient` вместо атрибута `DurableClient`, а вместо `IDurableOrchestrationClient`необходимо использовать тип параметра `DurableOrchestrationClient`. Дополнительные сведения о различиях между версиями см. в статье [устойчивые функции версии](durable-functions-versions.md) .

**Код JavaScript** Метод `purgeInstanceHistoryBy` можно использовать для условной очистки журнала экземпляров для нескольких экземпляров.

> [!NOTE]
> Чтобы успешно выполнить операцию очистки **журнала, состояние**среды выполнения целевого экземпляра должно быть **завершено**, завершено или **завершилось сбоем**.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Журнал экземпляра оркестрации можно очистить с помощью команды [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history`. Аналогично второму C# примеру в предыдущем разделе, он очищает журнал для всех экземпляров оркестрации, созданных в течение заданного интервала времени. Удаленные экземпляры можно фильтровать по состоянию выполнения. Команда имеет несколько параметров:

* **`created-after` (необязательный)** : удаление из журнала экземпляров, созданных после этой даты и времени в формате UTC. Принимаются значения даты и времени в формате ISO 8601.
* **`created-before` (необязательный)** : удаление из журнала экземпляров, созданных до этой даты и времени в формате UTC. Принимаются значения даты и времени в формате ISO 8601.
* **`runtime-status` (необязательно)** : Очистка журнала экземпляров с определенным состоянием (например, "выполняется" или "завершено"). Можно указать несколько состояний (через пробел).
* **`connection-string-setting` (необязательный)** : имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательно)** : имя используемого центра задач устойчивые функции. По умолчанию используется имя центра задач в файле [Host. JSON](durable-functions-bindings.md#host-json) .

Следующая команда удаляет журнал всех неудачных экземпляров, созданных до 14 ноября 2018 в 7:35 по Гринвичу (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Удаление центра задач

С помощью команды [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` можно удалить все артефакты хранилища, связанные с определенным центром задач, включая таблицы службы хранилища Azure, очереди и большие двоичные объекты. Команда имеет два параметра:

* **`connection-string-setting` (необязательный)** : имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательно)** : имя используемого центра задач устойчивые функции. По умолчанию используется имя центра задач в файле [Host. JSON](durable-functions-bindings.md#host-json) .

Следующая команда удаляет все данные службы хранилища Azure, связанные с `UserTest` концентратором задач.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Сведения об обработке управления версиями](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Встроенная Справочник по HTTP API для управления экземплярами](durable-functions-http-api.md)