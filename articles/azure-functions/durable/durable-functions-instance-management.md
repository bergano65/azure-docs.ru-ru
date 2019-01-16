---
title: Управление экземплярами в устойчивых функциях (Azure)
description: Сведения о том, как управлять экземплярами в расширении устойчивых функций для Функций Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 8dbf7b6f6741998972070234d90e87baca1154a4
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54042467"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Управление экземплярами в Устойчивых функциях в Azure

Экземпляры оркестрации [устойчивых функций](durable-functions-overview.md) можно запускать и завершать, а также отправлять в них запросы и события уведомления. Все операции управления экземпляром осуществляются с помощью [привязки клиента оркестрации](durable-functions-bindings.md). В этой статье подробно описаны все операции по управлению экземплярами.

## <a name="starting-instances"></a>Запуск экземпляров

Метод [StartNewAsync] (https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) из класса [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) или `startNew` из класса `DurableOrchestrationClient` (JavaScript) запускает новый экземпляр функции оркестратора. Экземпляры этого класса можно получить с помощью привязки `orchestrationClient`. На внутреннем уровне этот метод ставит в очередь управления сообщение, которое инициирует запуск функции с заданным именем и привязкой к триггеру `orchestrationTrigger`.

Эта асинхронная операция завершается после успешного планирования процесса оркестрации. Процесс оркестрации должен запуститься в течение 30 секунд. Если на это уходит больше времени, создается исключение `TimeoutException`.

> [!WARNING]
> При локальной разработке на языке JavaScript необходимо задать для переменной среды `WEBSITE_HOSTNAME` значение `localhost:<port>`, например `localhost:7071`, чтобы использовать методы для `DurableOrchestrationClient`. Дополнительные сведения об этом требовании см. в [описании проблемы на сайте GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) принимает следующие параметры:

* **Name**. Имя функции оркестратора, для которой назначается запуск.
* **Входные данные** Любые данные, сериализуемые в формат JSON, которые нужно передать в функцию оркестратора как входные данные.
* **InstanceId**. Уникальный идентификатор экземпляра (необязательно). Если идентификатор экземпляра не указан, ему будет присвоено случайное значение.

Ниже приведен простой пример на C#.

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

`startNew` принимает следующие параметры.

* **Name**. Имя функции оркестратора, для которой назначается запуск.
* **InstanceId**. Уникальный идентификатор экземпляра (необязательно). Если идентификатор экземпляра не указан, ему будет присвоено случайное значение.
* **Входные данные** Любые данные, сериализуемые в формат JSON, которые нужно передать в функцию оркестратора как входные данные (необязательно).

Ниже приведен простой пример JavaScript.

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!NOTE]
> Используйте случайный идентификатор экземпляра. Это помогает более равномерно распределять нагрузку при масштабировании функций оркестратора между несколькими виртуальными машинами. Указывать конкретные идентификаторы экземпляров имеет смысл в том случае, когда они должны поступать из внешнего источника, а также при реализации схемы [одноэлементного оркестратора](durable-functions-singletons.md).

### <a name="using-core-tools"></a>Использование Core Tools

Запустить экземпляр также можно с помощью команды [Azure Functions Core Tools](../functions-run-local.md) `durable start-new`. Она принимает следующие параметры.

* **`function-name` (обязательный)**. Имя запускаемой функции.
* **`input` (необязательный)**. Входные данные функции (в строке или в файле JSON). Поставьте в начале пути к файлу символ `@` (например, `@path/to/file.json`).
* **`id` (необязательный)**. Идентификатор экземпляра оркестрации. Если этот параметр не указан, создается случайный идентификатор GUID.
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. По умолчанию используется AzureWebJobsStorage.
* **`task-hub-name` (необязательный)**. Имя нужного центра устойчивых задач. По умолчанию используется DurableFunctionsHub. Его также можно задать в файле [host.json](durable-functions-bindings.md#host-json) через durableTask:HubName.

> [!NOTE]
> Предполагается, что команды Core Tools выполняются из корневого каталога приложения-функции. При явном указании параметров `connection-string-setting` и `task-hub-name` команды могут выполняться из любого каталога. Хотя эти команды можно выполнять без запуска хоста приложения-функции, некоторые результаты можно получить только после запуска хоста. Например, команда `start-new` поставит сообщение запуска в очередь целевого центра задач, но оркестрация будет запущена только после запуска хост-процесса приложения-функции, который может обработать сообщение.

Следующая команда запустит функцию HelloWorld и передаст ей содержимое файла counter-data.json:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="querying-instances"></a>Запросы к экземплярам

Метод [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) из класса [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) или метод `getStatus` из класса `DurableOrchestrationClient` (JavaScript) подает запрос на сведения о состоянии экземпляра оркестрации.

Он принимает параметры `instanceId` (обязательный), `showHistory` (необязательный), `showHistoryOutput` (необязательный) и `showInput` (необязательный, только для .NET).

* **`showHistory`**. Если задано значение `true`, ответ будет содержать журнал выполнения.
* **`showHistoryOutput`**. Если задано значение `true`, журнал выполнения будет содержать выходные данные действия.
* **`showInput`**. Если задано значение `false`, ответ не будет содержать входные данные функции. По умолчанию используется значение `true`. (Только для .NET.)

Метод возвращает объект JSON со следующими свойствами:

* **Name**. Имя функции оркестратора.
* **InstanceId**. Идентификатор экземпляра оркестрации (будет совпадать с переданным значением `instanceId`).
* **CreatedTime**. Время создания, то есть время начала выполнения функции оркестратора.
* **LastUpdatedTime**. Время прохождения последней контрольной точки оркестрации.
* **Входные данные** Входные данные функции в виде значения JSON. Это поле не заполняется, если `showInput` имеет значение false.
* **CustomStatus**. Настраиваемое значение состояния оркестрации в формате JSON.
* **Выходные данные**: Выходные данные функции в формате JSON (если выполнение функции успешно завершено). Если выполнение функции оркестратора завершилась сбоем, это свойство содержит сведения об ошибке. Если функция оркестратора была прервана, это свойство содержит сведения о причине завершения (при их наличии).
* **RuntimeStatus**. Одно из следующих значений:
  * **Pending**. Выполнение экземпляра было запланировано, но еще не начато.
  * **Running**. Началось выполнение экземпляра.
  * **Completed**. Выполнение экземпляра завершилось в обычном режиме.
  * **ContinuedAsNew**. Экземпляр выполнил перезапуск с очисткой журнала выполнения. Это переходное состояние.
  * **Failed**. Выполнение экземпляра завершилось ошибкой.
  * **Terminated**. Выполнение экземпляра было прервано.
* **History**. Журнал выполнения оркестрации. Это поле заполняется, только если `showHistory` имеет значение `true`.

Этот метод возвращает `null`, если экземпляр не существует или его выполнение еще не начато.

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="using-core-tools"></a>Использование Core Tools

Получить состояние экземпляра оркестрации также можно с помощью команды [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status`. Она принимает следующие параметры.

* **`id` (обязательный)**. Идентификатор экземпляра оркестрации.
* **`show-input` (необязательный)**. Если задано значение `true`, ответ будет содержать входные данные функции. По умолчанию используется значение `false`.
* **`show-output` (необязательный)**. Если задано значение `true`, ответ будет содержать выходные данные функции. По умолчанию используется значение `false`.
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. По умолчанию используется AzureWebJobsStorage.
* **`task-hub-name` (необязательный)**. Имя нужного центра устойчивых задач. По умолчанию используется DurableFunctionsHub. Его также можно задать в файле [host.json](durable-functions-bindings.md#host-json) через durableTask:HubName.

Указанная ниже команда получит состояние (включая входные и выходные данные) экземпляра с идентификатором 0ab8c55a66644d68a3a8b220b12d209c. Предполагается, что команда `func` выполняется из корневого каталога приложения-функции:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Команду `durable get-history` можно использовать для получения журнала экземпляра оркестрации. Она принимает следующие параметры.

* **`id` (обязательный)**. Идентификатор экземпляра оркестрации.
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. По умолчанию используется AzureWebJobsStorage.
* **`task-hub-name` (необязательный)**. Имя нужного центра устойчивых задач. По умолчанию используется DurableFunctionsHub. Его также можно задать в файле host.json через durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="querying-all-instances"></a>Запросы ко всем экземплярам

С помощью метода `GetStatusAsync` (.NET) или `getStatusAll` (JavaScript) вы можете запросить данные о состоянии всех экземпляров оркестрации. Чтобы отменить метод, в .NET вы можете передать объект `CancellationToken`. Метод возвращает объекты с теми же свойствами, что и метод `GetStatusAsync` с параметрами.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

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

### <a name="using-core-tools"></a>Использование Core Tools

Запрашивать состояния экземпляров также можно с помощью команды [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances`. Она принимает следующие параметры.

* **`top` (необязательный)**. Эта команда поддерживает разбиение на страницы. Этот параметр соответствует количеству экземпляров, получаемых по запросу. По умолчанию задано значение 10.
* **`continuation-token` (необязательный)**. Маркер, указывающий извлекаемую страницу или раздел экземпляров. Каждое выполнение `get-instances` возвращает маркер для извлечения следующего набора экземпляров.
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. По умолчанию используется AzureWebJobsStorage.
* **`task-hub-name` (необязательный)**. Имя нужного центра устойчивых задач. По умолчанию используется DurableFunctionsHub. Его также можно задать в файле [host.json](durable-functions-bindings.md#host-json) через durableTask:HubName.

```bash
func durable get-instances
```

## <a name="querying-instances-with-filters"></a>Запросы к экземплярам с фильтрами

Используйте метод `GetStatusAsync` (.NET) или `getStatusBy` (JavaScript), чтобы создать список экземпляров оркестрации, который соответствует набору предопределенных фильтров. Среди возможных параметров фильтра — время создания оркестрации и состояние среды выполнения оркестрации.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
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

### <a name="using-the-functions-core-tools"></a>Использование Functions Core Tools

Команду `durable get-instances` также можно использовать с фильтрами. В дополнение к упомянутым выше параметрам `top`, `continuation-token`, `connection-string-setting` и `task-hub-name` можно использовать три параметра фильтра (`created-after`, `created-before` и `runtime-status`).

* **`created-after` (необязательный)**. Получение экземпляров, созданных после этой даты и времени в формате UTC. Принимаются значения даты и времени в формате ISO 8601.
* **`created-before` (необязательный)**. Получение экземпляров, созданных до этой даты и времени в формате UTC. Принимаются значения даты и времени в формате ISO 8601.
* **`runtime-status` (необязательный)**. Получение экземпляров с указанными состояниями ("выполняется", "выполнено" и т. д.). Можно указать несколько состояний (через пробел).
* **`top` (необязательный)**. Количество экземпляров, получаемых по запросу. По умолчанию задано значение 10.
* **`continuation-token` (необязательный)**. Маркер, указывающий извлекаемую страницу или раздел экземпляров. Каждое выполнение `get-instances` возвращает маркер для извлечения следующего набора экземпляров.
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. По умолчанию используется AzureWebJobsStorage.
* **`task-hub-name` (необязательный)**. Имя нужного центра устойчивых задач. По умолчанию используется DurableFunctionsHub. Его также можно задать в файле [host.json](durable-functions-bindings.md#host-json) через durableTask:HubName.

Если фильтры (`created-after`, `created-before` или `runtime-status`) не указаны, экземпляры `top` будут извлекаться без учета состояния среды выполнения или времени создания.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminating-instances"></a>Прерывание выполнения экземпляров

Запуск экземпляра оркестрации можно прервать с помощью метода [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) из класса [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) или метода `terminate` из класса `DurableOrchestrationClient` (JavaScript). Он принимает два параметра: `instanceId` и строку `reason`, которая сохраняется в журнале выполнения и в сведениях о состоянии экземпляра. Выполнение экземпляра прекращается по достижении очередной точки ожидания `await` (.NET) или `yield` (JavaScript). Если он уже находится в состоянии ожидания `await` (.NET) или `yield` (JavaScript), выполнение прекращается немедленно.

### <a name="c"></a>C#

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

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
> Завершение работы экземпляр в настоящее время не распространяется. Функции действий и суборкестрации будут выполняться до конца, даже если экземпляр оркестрации, вызвавший их, будет завершен.

### <a name="using-core-tools"></a>Использование Core Tools

Завершить работу экземпляра оркестрации также можно с помощью команды [Core Tools](../functions-run-local.md) `durable terminate`. Она принимает следующие параметры.

* **`id` (обязательный)**. Идентификатор экземпляра оркестрации, работу которого необходимо завершить.
* **`reason` (необязательный)**. Причина завершения.
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. По умолчанию используется AzureWebJobsStorage.
* **`task-hub-name` (необязательный)**. Имя нужного центра устойчивых задач. По умолчанию используется DurableFunctionsHub. Его также можно задать в файле [host.json](durable-functions-bindings.md#host-json) через durableTask:HubName.

Указанная ниже команда завершит работу экземпляра оркестрации с идентификатором 0ab8c55a66644d68a3a8b220b12d209c.

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="sending-events-to-instances"></a>Отправка событий в экземпляры

В выполняющиеся экземпляры можно отсылать уведомления о событиях с помощью метода [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) из класса [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) или метода `raiseEvent` из класса `DurableOrchestrationClient` (JavaScript). Чтобы обрабатывать эти уведомления, экземпляр должен ожидать вызова при помощи функции [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) или `waitForExternalEvent` (JavaScript).

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) и `raiseEvent` (JavaScript) принимают следующие параметры:

* **InstanceId**. Уникальный идентификатор экземпляра.
* **EventName**. Имя отправляемого события.
* **EventData**. Данные полезной нагрузки в формате JSON, отправляемые в экземпляр.

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!WARNING]
> Если экземпляра оркестрации с указанным *идентификатором* не существует или этот экземпляр не ожидает указанное *имя события*, сообщение о событии игнорируется. Дополнительные сведения об этом поведении см. в [описании проблемы на GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="using-core-tools"></a>Использование Core Tools

Породить событие также можно с помощью команды [Core Tools](../functions-run-local.md) `durable raise-event`. Она принимает следующие параметры.

* **`id` (обязательный)**. Идентификатор экземпляра оркестрации.
* **`event-name` (необязательный)**. Имя порождаемого события. Значение по умолчанию — `$"Event_{RandomGUID}"`.
* **`event-data` (необязательный)**. Данные, отправляемые в экземпляр оркестрации. Можно указать путь к файлу JSON, данные также могут быть указаны прямо в командной строке.
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. По умолчанию используется AzureWebJobsStorage.
* **`task-hub-name` (необязательный)**. Имя нужного центра устойчивых задач. По умолчанию используется DurableFunctionsHub. Его также можно задать в файле [host.json](durable-functions-bindings.md#host-json) через durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Дождитесь, пока завершится оркестрация.

Класс [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) предоставляет API-интерфейс [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) на .NET, который позволяет синхронно получать фактические выходные данные от экземпляра оркестрации. В JavaScript класс `DurableOrchestrationClient` предоставляет API-интерфейс `waitForCompletionOrCreateCheckStatusResponse` для той же цели. Эти методы по умолчанию используют значения 10 секунд для `timeout` и 1 секунду для `retryInterval`, если вы не укажете свои значения.  

Ниже приведен пример функции HTTP-триггера, в котором показано, как использовать этот API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Следующая строка вызывает эту функцию с временем ожидания в 2 секунды и интервалом повторов 0,5 секунды:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

В зависимости от того, сколько времени уйдет на получение ответа от экземпляра оркестрации, возможны два варианта развития событий.

* Если экземпляр оркестрации завершит работу до истечения указанного времени ожидания (2 секунды в нашем примере), то фактический ответ от экземпляра оркестрации доставляется синхронно:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* Если экземпляр оркестрации не успеет завершить работу до истечения указанного времени ожидания (2 секунды в нашем примере), возвращается ответ по умолчанию, как указано в **обнаружении URL-адреса API HTTP**:

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Формат URL-адресов веб-перехватчиков может отличаться в зависимости от того, какая версия узла Функций Azure выполняется. Описанный выше пример предназначен для узла Функций Azure 2.x.

## <a name="retrieving-http-management-webhook-urls"></a>Получение URL-адресов веб-перехватчика для управления HTTP

Внешние системы могут взаимодействовать с устойчивыми функциями через URL-адреса веб-перехватчиков, которые содержаться в ответе по умолчанию, описанном в разделе [Обнаружение URL-адреса API HTTP](durable-functions-http-api.md). Но доступ к URL-адресам веб-перехватчиков можно получить и программными средствами в клиенте оркестрации или функции действия с помощью метода [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) класса [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) или метода `createHttpManagementPayload` класса `DurableOrchestrationClient` (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) и `createHttpManagementPayload` имеют один параметр.

* **instanceId**. Уникальный идентификатор экземпляра.

Методы возвращают экземпляр [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) или объект (JavaScript) со следующими строковыми свойствами.

* **Id**. Идентификатор экземпляра оркестрации (будет совпадать с переданным значением `InstanceId`).
* **StatusQueryGetUri**. URL-адрес состояния экземпляра оркестрации.
* **SendEventPostUri**. URL-адрес вызова события экземпляра оркестрации.
* **TerminatePostUri**. URL-адрес завершения экземпляра оркестрации.
* **RewindPostUri**. URL-адрес перемотки экземпляра оркестрации.

Функции действий позволяют отправлять экземпляры этих объектов во внешние системы, чтобы отслеживать или вызывать события для оркестрации.

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
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

## <a name="rewinding-instances-preview"></a>Возврат состояния экземпляров (предварительная версия)

Сбойный экземпляр оркестрации можно *вернуть* к предыдущему исправному состоянию, используя программный интерфейс [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) или `rewindAsync` (JavaScript). Он возвращает процесс оркестрации в состояние *Running*, повторно запускает действие и/или ошибки выполнения суборкестрации, которые привели к сбою процесса оркестрации.

> [!NOTE]
> Этот API не является заменой правильной политики повторов и обработки ошибок. Вместо этого его следует применять исключительно в тех случаях, когда причини сбоя экземпляра оркестрации неизвестные. Дополнительные сведения о политике повторов и обработке ошибок см. в разделе [Обработка ошибок в устойчивых функциях (Функции Azure)](durable-functions-error-handling.md).

Вариант использования *возврата* — рабочий процесс, состоящий из ряда [человеческих утверждений](durable-functions-concepts.md#human). Предположим, существует ряд функций действий, уведомляющих кого-то, что требуется утверждение, и ожидающих ответа в режиме реального времени. Когда все действия утверждения завершаются получением ответов или истекает время ожидания их выполнения, происходит сбой другого действия из-за ошибки настройки приложения (например, недопустимая строка подключения к базе данных). Как результат — сбой оркестрации в рабочем процессе. С помощью программного интерфейса `RewindAsync` (.NET) или `rewindAsync` (JavaScript) администратор приложения может исправить ошибку конфигурации и *возвратить* к исходному состоянию сбойный экземпляр непосредственно перед сбоем. Ни один из шагов взаимодействия с человеком не нуждается в повторном утверждении, и оркестрацию теперь можно успешно завершить.

> [!NOTE]
> Функция *возврата* не поддерживает экземпляры возвратной оркестрации, использующие устойчивые таймеры.

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (только для решения "Функции" версии 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="using-core-tools"></a>Использование Core Tools

Перемотать экземпляр оркестрации назад также можно с помощью команды [Core Tools](../functions-run-local.md) `durable rewind`. Она принимает следующие параметры.

* **`id` (обязательный)**. Идентификатор экземпляра оркестрации.
* **`reason` (необязательный)**. Причина перемотки экземпляра оркестрации.
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. По умолчанию используется AzureWebJobsStorage.
* **`task-hub-name` (необязательный)**. Имя нужного центра устойчивых задач. По умолчанию используется DurableFunctionsHub. Его также можно задать в файле [host.json](durable-functions-bindings.md#host-json) через durableTask:HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Очистка журнала экземпляра

> [!NOTE]
> API `PurgeInstanceHistoryAsync` в настоящее время доступен только для C#. Экземпляр для JavaScript будет добавлен в следующем выпуске.

Журнал оркестрации можно очистить с помощью функции [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_). Функция удалит все данные, связанные с оркестрацией, — строки таблицы Azure и большие двоичные объекты сообщений (при наличии). У метода есть две перегрузки. Первая очищает журнал по идентификатору экземпляра оркестрации:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

Во втором примере показана активируемая по таймеру функция, которая удаляет из журнала все экземпляры оркестрации, выполненные после указанного интервала времени. В этом случае она удалит данные обо всех экземплярах, выполненных более 30 дней назад. Она запускается один раз в день в 00:00:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
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
> Перегрузка *PurgeInstanceHistory*, которая принимает как параметр период времени, будет обрабатывать только экземпляры оркестрации с состоянием среды выполнения "Выполнено", "Завершено" или "Сбой".

### <a name="using-core-tools"></a>Использование Core Tools

Очистить журнал оркестрации можно с помощью команды [Core Tools](../functions-run-local.md) `durable purge-history`. Как и во втором примере C# выше, она удаляет из журнала все экземпляры оркестрации, созданные в течение указанного интервала времени. Очищаемые экземпляры можно дополнительно отфильтровать по состоянию среды выполнения. Команда имеет несколько параметров:

* **`created-after` (необязательный)**. Удаление из журнала экземпляров, созданных после этой даты и времени в формате UTC. Принимаются значения даты и времени в формате ISO 8601.
* **`created-before` (необязательный)**. Удаление из журнала экземпляров, созданных до этой даты и времени в формате UTC. Принимаются значения даты и времени в формате ISO 8601.
* **`runtime-status` (необязательный)**. Удаление из журнала экземпляров с указанными состояниями ("выполняется", "выполнено" и т. д.). Можно указать несколько состояний (через пробел).
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. По умолчанию используется AzureWebJobsStorage.
* **`task-hub-name` (необязательный)**. Имя нужного центра устойчивых задач. По умолчанию используется DurableFunctionsHub. Его также можно задать в файле [host.json](durable-functions-bindings.md#host-json) через durableTask:HubName.

Указанная ниже команда удалит из журнала все невыполненные экземпляры, созданные до 14 ноября 2018 г., в 19:35 по времени в формате UTC.

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="deleting-a-task-hub"></a>Удаление центра задач

Используя команду [Core Tools](../functions-run-local.md) `durable delete-task-hub`, можно удалить все артефакты хранения, связанные с конкретным центром задач. К ним относятся таблицы, очереди и большие двоичные объекты службы хранилища Azure. Команда имеет два параметра:

* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. По умолчанию используется AzureWebJobsStorage.
* **`task-hub-name` (необязательный)**. Имя нужного центра устойчивых задач. По умолчанию используется DurableFunctionsHub. Его также можно задать в файле [host.json](durable-functions-bindings.md#host-json) через durableTask:HubName.

Следующая команда удалит из службы хранилища Azure все данные, связанные с центром задач UserTest.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> Узнайте, как использовать [HTTP-интерфейсы API для управления экземплярами](durable-functions-http-api.md)
