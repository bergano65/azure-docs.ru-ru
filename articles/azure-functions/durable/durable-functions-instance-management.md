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
origin.date: 12/07/2018
ms.date: 03/19/2019
ms.author: v-junlch
ms.openlocfilehash: ee96bc5e17051ab37be34eecbb8e4fe35599cd5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730775"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Управление экземплярами в Устойчивых функциях в Azure

Если вы используете [устойчивых функций](durable-functions-overview.md) расширение для функций Azure, или хотите запустить, таким образом, убедитесь, что, вы получаете максимально эффективно использовать его. Экземпляры оркестрации устойчивых функций можно оптимизировать, Дополнительные сведения об управлении ими. В этой статье подробно описаны все операции по управлению экземплярами.

Можно начать прерывает экземпляры, например, и вы можете запросить экземплярами, включая возможность запросить все экземпляры и экземпляры запросов с фильтрами. Кроме того можно отправлять события в экземпляры, дожидаться выполнения оркестрации и получить URL-адреса веб-перехватчика управления HTTP. В этой статье рассматриваются другие операции управления, кроме того, включая Перемотка экземпляров, очистка журнал экземпляра и удаление центр задач.

В устойчивых функциях у вас есть параметры, как требуется для реализации каждой из этих операций управления. Эта статья содержит примеры, использующие [основных инструментов функций Azure](../functions-run-local.md) для обоих .NET (C#) и JavaScript.

## <a name="start-instances"></a>Запуск экземпляров

Очень важно иметь возможность запуска экземпляра оркестрации. Обычно это делается при использовании привязки устойчивых функций в другую функцию триггера.

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) метод [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) или `startNew` на `DurableOrchestrationClient` (JavaScript) запускает новый экземпляр. Получить экземпляры этого класса с помощью `orchestrationClient` привязки. На внутреннем уровне этот метод ставит в очередь управления сообщение, которое инициирует запуск функции с заданным именем и привязкой к триггеру `orchestrationTrigger`.

Эта асинхронная операция завершается после успешного планирования процесса оркестрации. Процесс оркестрации должен запуститься в течение 30 секунд. Если он занимает больше времени, вы увидите `TimeoutException`.

> [!WARNING]
> При локальной разработке на языке JavaScript, задайте переменную среды `WEBSITE_HOSTNAME` для `localhost:<port>` (например, `localhost:7071`) использовать методы на `DurableOrchestrationClient`. Дополнительные сведения об этом требовании см. в [описании проблемы на сайте GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) принимает следующие параметры:

* **Name**. Имя функции оркестратора, для которой назначается запуск.
* **Входные данные** Любые данные, сериализуемые в формат JSON, которые нужно передать в функцию оркестратора как входные данные.
* **InstanceId**. Уникальный идентификатор экземпляра (необязательно). Если этот параметр не задан, данный метод использует случайный идентификатор.

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
* **InstanceId**. Уникальный идентификатор экземпляра (необязательно). Если этот параметр не задан, данный метод использует случайный идентификатор.
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

> [!TIP]
> Используйте случайный идентификатор экземпляра. Это помогает обеспечить равное распределение нагрузки при при масштабировании функций оркестратора между несколькими виртуальными машинами. Подходящее время для использования идентификаторов не случайно выбранный экземпляр является, когда должны поступать из внешнего источника, или когда вы реализуете [одноэлементного оркестратора](durable-functions-singletons.md) шаблон.

### <a name="azure-functions-core-tools"></a>Основных инструментов функций Azure

Можно также запустить экземпляр напрямую с помощью [основных инструментов функций Azure](../functions-run-local.md) `durable start-new` команды. Она принимает следующие параметры.

* **`function-name` (обязательный)**. Имя функции для запуска.
* **`input` (необязательный)**. Входные данные, функции, либо в процессе или JSON-файле. Для файлов, добавьте префикс к пути к файлу с `@`, такие как `@path/to/file.json`.
* **`id` (необязательный)**. Идентификатор экземпляра оркестрации. Если этот параметр не задан, команда использует случайный идентификатор GUID.
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. По умолчанию используется AzureWebJobsStorage.
* **`task-hub-name` (необязательный)**. Имя центра задач устойчивых функций для использования. По умолчанию используется DurableFunctionsHub. Вы можете также задать в [host.json](durable-functions-bindings.md#host-json) с помощью durableTask:HubName.

> [!NOTE]
> Основные средства команды предполагается, что вы используете их в корневом каталоге приложения-функции. Если указаны явным образом `connection-string-setting` и `task-hub-name` параметров, можно выполнить команды из любого каталога. Несмотря на то, что эти команды могут выполняться без узел приложения функции, под управлением, может оказаться, что не может отслеживать некоторые эффекты, если узел не работает. Например `start-new` команда помещает в очередь сообщение запуска в центр задач целевой объект, но оркестрации не выполнялся при отсутствии функции приложения хост-процессе под управлением, который может обработать сообщение.

Следующая команда запускает функцию с именем HelloWorld и передает содержимое файла `counter-data.json` к нему:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Экземпляры запросов

Как часть вашего усилий в управлении ваших оркестраций скорее всего необходимо собирать информацию о состоянии экземпляра оркестрации (например, ли он завершилась нормально или с ошибкой).

Метод [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) из класса [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) или метод `getStatus` из класса `DurableOrchestrationClient` (JavaScript) подает запрос на сведения о состоянии экземпляра оркестрации.

Он принимает параметры `instanceId` (обязательный), `showHistory` (необязательный), `showHistoryOutput` (необязательный) и `showInput` (необязательный, только для .NET).

* **`showHistory`**. Если значение `true`, ответ содержит журнал выполнения.
* **`showHistoryOutput`**. Если значение `true`, журнал выполнения содержит выходные данные действия.
* **`showInput`**. Если значение `false`, ответ не будет содержать входные данные функции. По умолчанию используется значение `true`. (Только для .NET.)

Метод возвращает объект JSON со следующими свойствами:

* **Name**. Имя функции оркестратора.
* **InstanceId**. Идентификатор экземпляра оркестрации (будет совпадать с переданным значением `instanceId`).
* **CreatedTime**. Время создания, то есть время начала выполнения функции оркестратора.
* **LastUpdatedTime**. Время прохождения последней контрольной точки оркестрации.
* **Входные данные** Входные данные функции в виде значения JSON. Это поле не заполнено, если `showInput` имеет значение false.
* **CustomStatus**. Настраиваемое значение состояния оркестрации в формате JSON.
* **Выходные данные**: Выходные данные функции в формате JSON (если выполнение функции успешно завершено). Если не удалось выполнить функцию оркестратора, это свойство содержит сведения об ошибке. Если функция оркестратора была прервана, это свойство включает причины завершения (при наличии).
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

### <a name="azure-functions-core-tools"></a>Основных инструментов функций Azure

Можно также для получения состояния экземпляра оркестрации напрямую, с помощью [основных инструментов функций Azure](../functions-run-local.md) `durable get-runtime-status` команды. Она принимает следующие параметры.

* **`id` (обязательный)**. Идентификатор экземпляра оркестрации.
* **`show-input` (необязательный)**. Если значение `true`, ответ содержит входные данные функции. По умолчанию используется значение `false`.
* **`show-output` (необязательный)**. Если значение `true`, ответ содержит выходные данные функции. Значение по умолчанию — `false`.
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)**. Имя центра задач устойчивых функций для использования. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать [host.json](durable-functions-bindings.md#host-json), с помощью durableTask:HubName.

Следующая команда извлекает состояние экземпляра с Идентификатором экземпляра оркестрации 0ab8c55a66644d68a3a8b220b12d209c (включая входные и выходные данные). Предполагается, что вы используете `func` команду из корневого каталога приложения-функции:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Можно использовать `durable get-history` команду, чтобы получить журнал экземпляра оркестрации. Она принимает следующие параметры.

* **`id` (обязательный)**. Идентификатор экземпляра оркестрации.
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)**. Имя центра задач устойчивых функций для использования. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать в файле host.json, с помощью durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Запросить все экземпляры

Вместо того чтобы запрос один экземпляр во взаимодействии одновременно может оказаться более эффективным, чтобы запросить все из них за один раз.

С помощью метода `GetStatusAsync` (.NET) или `getStatusAll` (JavaScript) вы можете запросить данные о состоянии всех экземпляров оркестрации. В .NET, можно передать `CancellationToken` объекта в случае, если вы хотите отменить его. Метод возвращает объекты с теми же свойствами, что и метод `GetStatusAsync` с параметрами.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
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

### <a name="azure-functions-core-tools"></a>Основных инструментов функций Azure

Имеется также возможность напрямую, экземпляры запросов с помощью [основных инструментов функций Azure](../functions-run-local.md) `durable get-instances` команды. Она принимает следующие параметры.

* **`top` (необязательный)**. Эта команда поддерживает разбиение на страницы. Этот параметр соответствует количеству экземпляров, получаемых по запросу. По умолчанию задано значение 10.
* **`continuation-token` (необязательный)**. Токен, чтобы указать, какая страница или части извлекаемых экземпляров. Каждое выполнение `get-instances` возвращает маркер для извлечения следующего набора экземпляров.
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)**. Имя центра задач устойчивых функций для использования. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать [host.json](durable-functions-bindings.md#host-json), с помощью durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Экземпляры запросов с фильтрами

Что делать, если вам не обязательно всю информацию, которую может предоставить стандартного экземпляра запроса? Например что делать, если вы просто ищете время создания оркестрации или состояние среды выполнения оркестрации? Можно сузить запрос, применяя фильтры.

Используйте `GetStatusAsync` (.NET) или `getStatusBy` метод (JavaScript), чтобы получить список экземпляров оркестрации, соответствующих набору встроенные фильтры.

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

### <a name="azure-functions-core-tools"></a>Основных инструментов функций Azure

В основных инструментов функций Azure можно также использовать `durable get-instances` команду с фильтрами. Помимо упомянутых выше `top`, `continuation-token`, `connection-string-setting`, и `task-hub-name` параметров, можно использовать три параметры фильтра (`created-after`, `created-before`, и `runtime-status`).

* **`created-after` (необязательный)**. Получение экземпляров, созданных после этой даты и времени в формате UTC. Принимаются значения даты и времени в формате ISO 8601.
* **`created-before` (необязательный)**. Получение экземпляров, созданных до этой даты и времени в формате UTC. Принимаются значения даты и времени в формате ISO 8601.
* **`runtime-status` (необязательный)**. Получить экземпляры с определенным состоянием (например, запущенных или завершенных). Можно указать несколько состояний (через пробел).
* **`top` (необязательный)**. Количество экземпляров, получаемых по запросу. По умолчанию задано значение 10.
* **`continuation-token` (необязательный)**. Токен, чтобы указать, какая страница или части извлекаемых экземпляров. Каждое выполнение `get-instances` возвращает маркер для извлечения следующего набора экземпляров.
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)**. Имя центра задач устойчивых функций для использования. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать [host.json](durable-functions-bindings.md#host-json), с помощью durableTask:HubName.

Если не указать какие-либо фильтры (`created-after`, `created-before`, или `runtime-status`), команда просто извлекает `top` экземпляров, независимо от времени состояние или создания среды выполнения.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Прерывает экземпляры

Если у вас есть экземпляр оркестрации, которая занимает слишком много времени для запуска, или необходимо остановить ее до его завершения по любой причине, вы можете завершить работу.

Можно использовать [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) метод [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) классов (.NET), или `terminate` метод `DurableOrchestrationClient` класса (JavaScript). Два параметра `instanceId` и `reason` строку, которая записываются в журналы и состояние экземпляра. Экземпляра прекращается останавливается сразу же по достижении очередной `await` (.NET) или `yield` точки (JavaScript), либо сразу же завершает операцию, если он уже `await` или `yield`.

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
> Завершение работы экземпляров не распространяет в настоящее время. Функции действий и суборкестрации выполняться до завершения, независимо от того, завершили экземпляр оркестрации, вызвавшей их.

### <a name="azure-functions-core-tools"></a>Основных инструментов функций Azure

Можно также завершить экземпляр оркестрации напрямую, с помощью [основных инструментов функций Azure](../functions-run-local.md) `durable terminate` команды. Она принимает следующие параметры.

* **`id` (обязательный)**. Идентификатор экземпляра оркестрации для завершения.
* **`reason` (необязательный)**. Причина завершения.
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)**. Имя центра задач устойчивых функций для использования. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать [host.json](durable-functions-bindings.md#host-json), с помощью durableTask:HubName.

Следующая команда завершает экземпляр оркестрации с Идентификатором 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Отправка событий в экземпляры

В некоторых случаях важно для вашей функции оркестратора иметь возможность ожидания и передачи внешних событий. Сюда входят [мониторинг функций](durable-functions-concepts.md#monitoring) и функции, которые ожидают [действий человека](durable-functions-concepts.md#human).

Отправлять уведомления о событиях в выполняющиеся экземпляры с помощью [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) метод [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) классов (.NET) или `raiseEvent` метод `DurableOrchestrationClient` класса () JavaScript). Чтобы обрабатывать эти уведомления, экземпляр должен ожидать вызова при помощи функции [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) или `waitForExternalEvent` (JavaScript).

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

> [!IMPORTANT]
> Если нет экземпляра оркестрации с заданным Идентификатором экземпляра, или если экземпляр не ожидает имя заданного события, сообщения о событии удаляется. Дополнительные сведения об этом поведении см. в [описании проблемы на GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="azure-functions-core-tools"></a>Основных инструментов функций Azure

Можно также вызвать событие в экземпляр оркестрации напрямую, с помощью [основных инструментов функций Azure](../functions-run-local.md) `durable raise-event` команды. Она принимает следующие параметры.

* **`id` (обязательный)**. Идентификатор экземпляра оркестрации.
* **`event-name` (необязательный)**. Имя порождаемого события. Значение по умолчанию — `$"Event_{RandomGUID}"`.
* **`event-data` (необязательный)**. Данные, отправляемые в экземпляр оркестрации. Это может быть путь к JSON-файл, или вы можете предоставить данные непосредственно в командной строке.
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)**. Имя центра задач устойчивых функций для использования. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать [host.json](durable-functions-bindings.md#host-json), с помощью durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Дождитесь, пока завершится оркестрация.

Длительными оркестрациями вы можете ожидать и получить результаты оркестрации. В таких случаях также полезно иметь возможность определить период времени ожидания в отношении оркестрации. Если превышено, состояние оркестрации должно быть возвращено вместо результатов.

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) предоставляет [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API в .NET. Этот API позволяет синхронно получать фактические выходные данные от экземпляра оркестрации. В JavaScript класс `DurableOrchestrationClient` предоставляет API-интерфейс `waitForCompletionOrCreateCheckStatusResponse` для той же цели. Если они не заданы, в методах используется значение по умолчанию 10 секунд для `timeout`и 1 секунду для `retryInterval`.  

Ниже приведен пример функции HTTP-триггера, в котором показано, как использовать этот API:

```C#
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace VSSample
{
    public static class HttpSyncStart
    {
        private const string Timeout = "timeout";
        private const string RetryInterval = "retryInterval";

        [FunctionName("HttpSyncStart")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/wait")]
            HttpRequestMessage req,
            [OrchestrationClient] DurableOrchestrationClientBase starter,
            string functionName,
            ILogger log)
        {
            // Function input comes from the request content.
            dynamic eventData = await req.Content.ReadAsAsync<object>();
            string instanceId = await starter.StartNewAsync(functionName, eventData);

            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

            TimeSpan timeout = GetTimeSpan(req, Timeout) ?? TimeSpan.FromSeconds(30);
            TimeSpan retryInterval = GetTimeSpan(req, RetryInterval) ?? TimeSpan.FromSeconds(1);
            
            return await starter.WaitForCompletionOrCreateCheckStatusResponseAsync(
                req,
                instanceId,
                timeout,
                retryInterval);
        }

        private static TimeSpan? GetTimeSpan(HttpRequestMessage request, string queryParameterName)
        {
            string queryParameterStringValue = request.RequestUri.ParseQueryString()[queryParameterName];
            if (string.IsNullOrEmpty(queryParameterStringValue))
            {
                return null;
            }

            return TimeSpan.FromSeconds(double.Parse(queryParameterStringValue));
        }
    }
}
```

```Javascript
const df = require("durable-functions");

const timeout = "timeout";
const retryInterval = "retryInterval";

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = await client.startNew(req.params.functionName, undefined, req.body);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    const timeoutInMilliseconds = getTimeInSeconds(req, timeout) || 30000;
    const retryIntervalInMilliseconds = getTimeInSeconds(req, retryInterval) || 1000;

    return client.waitForCompletionOrCreateCheckStatusResponse(
        context.bindingData.req,
        instanceId,
        timeoutInMilliseconds,
        retryIntervalInMilliseconds);
};

function getTimeInSeconds (req, queryParameterName) {
    const queryValue = req.query[queryParameterName];
    return queryValue
        ? queryValue // expected to be in seconds
        * 1000 : undefined;
}
```

Вызовите функцию со следующей строкой. Используйте 2 секунд времени ожидания и 0,5 секунд интервал повторных попыток:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

В зависимости от того, сколько времени уйдет на получение ответа от экземпляра оркестрации, возможны два варианта развития событий.

* Экземпляры оркестрации завершается в течение указанного времени ожидания (в данном случае 2 секунды), и ответ доступен фактического экземпляра оркестрации доставляется, синхронно:

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

* Экземпляры оркестрации не завершается в течение указанного времени ожидания, и ответ доступен умолчанию, как указано в [URL-адрес HTTP API обнаружения](durable-functions-http-api.md):

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
> Формат URL-адреса веб-перехватчика могут отличаться в зависимости от того, какая версия узла функций Azure выполняется. Описанный выше пример предназначен для узла Функций Azure 2.x.

## <a name="retrieve-http-management-webhook-urls"></a>Получить URL-адреса веб-перехватчика HTTP управления

Внешней системы можно использовать для отслеживания или вызывать события в согласование. Внешние системы могут взаимодействовать с устойчивых функций через URL-адреса веб-перехватчика, которые являются частью ответ по умолчанию, описанные в [URL-адрес HTTP API обнаружения](durable-functions-http-api.md). Тем не менее URL-адреса веб-перехватчика также может осуществляться программными средствами в клиенте оркестрации или в функции действия. Это можно сделать с помощью [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) метод [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) классов (.NET), или `createHttpManagementPayload` метод `DurableOrchestrationClient` класса (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) и `createHttpManagementPayload` имеют один параметр.

* **instanceId**. Уникальный идентификатор экземпляра.

Методы возвращают экземпляр [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) или объект (JavaScript), со следующими свойствами строки:

* **Id:** Идентификатор экземпляра оркестрации (будет совпадать с переданным значением `InstanceId`).
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

## <a name="rewind-instances-preview"></a>REWIND экземпляров (Предварительная версия)

Если у вас возникнет ошибка оркестрации непредвиденной причине, вы можете *rewind* экземпляра в ранее работоспособное состояние с помощью API, созданные для этой цели.

> [!NOTE]
> Этот API не является заменой правильной политики повторов и обработки ошибок. Вместо этого его следует применять исключительно в тех случаях, когда причини сбоя экземпляра оркестрации неизвестные. Дополнительные сведения о политики повторов и обработки ошибок, см. в разделе [обработка ошибок](durable-functions-error-handling.md) раздела.

Используйте [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) или `rewindAsync` API (JavaScript), чтобы поместить оркестрации обратно в *под управлением* состояния. Повторно обработать срез ошибками выполнения действия или suborchestration причина сбоя оркестрации.

Например, предположим, у вас есть рабочий процесс, состоящие из ряда [человека утверждения](durable-functions-concepts.md#human). Предположим, что существует ряд функций действий, которые уведомляют другим лицам, утверждение требуется и ожидания в режиме реального времени ответа. В конце концов действий утверждения получили ответы или истекло время ожидания, предположим, что другой действие завершается неудачно из-за неправильной настройки приложения, такие как строки подключения к неверной базы данных. Как результат — сбой оркестрации в рабочем процессе. С помощью `RewindAsync` (.NET) или `rewindAsync` API, приложения (JavaScript) администратор может устранить ошибки конфигурации и rewind неудачных оркестрации в состояние непосредственно перед сбоем. Ни одно из действий взаимодействия человека с должны быть повторно одобрен и теперь можно выполнить оркестрацию.

> [!NOTE]
> *Rewind* Перемотка экземпляров оркестрации, использовать устойчивые таймеры не поддерживаются функцией.

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

### <a name="azure-functions-core-tools"></a>Основных инструментов функций Azure

Также можно перемотать экземпляр оркестрации напрямую с помощью [основных инструментов функций Azure](../functions-run-local.md) `durable rewind` команды. Она принимает следующие параметры.

* **`id` (обязательный)**. Идентификатор экземпляра оркестрации.
* **`reason` (необязательный)**. Причина для перемотки экземпляра оркестрации.
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)**. Имя центра задач устойчивых функций для использования. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать [host.json](durable-functions-bindings.md#host-json), с помощью durableTask:HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Очистить журнал экземпляра

Чтобы удалить все данные, связанные с согласование, могут удалять из журнала экземпляра. Например можно избавиться от строк таблицы Azure и больших двоичных объектов больших сообщений, если они существуют. Чтобы сделать это, используйте [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) API.

> [!NOTE]
> API `PurgeInstanceHistoryAsync` в настоящее время доступен только для C#.

 У метода есть две перегрузки. Первый из них очищает журнал с помощью идентификатора экземпляра оркестрации:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

Во втором примере показана активируемая по таймеру функция, которая удаляет из журнала все экземпляры оркестрации, выполненные после указанного интервала времени. В этом случае она удаляет данные для всех экземпляров, завершения более 30 дней назад. Расписанию оно должно выполняться раз в день в Полдень:

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
> Для успешного выполнения процесса функцию, активируемую по времени состояние среды выполнения должен быть **завершено**, **Terminated**, или **Failed**.

### <a name="azure-functions-core-tools"></a>Основных инструментов функций Azure

Можно очистить журнал экземпляра оркестрации с помощью [основных инструментов функций Azure](../functions-run-local.md) `durable purge-history` команды. Аналогично второй C# примере в предыдущем разделе, он выполняет очистку журнала для всех экземпляров оркестрации, созданные во время указанного временного интервала. Удаленных экземпляров можно отфильтровать по состоянию среды выполнения. Команда имеет несколько параметров:

* **`created-after` (необязательный)**. Удаление из журнала экземпляров, созданных после этой даты и времени в формате UTC. Принимаются значения даты и времени в формате ISO 8601.
* **`created-before` (необязательный)**. Удаление из журнала экземпляров, созданных до этой даты и времени в формате UTC. Принимаются значения даты и времени в формате ISO 8601.
* **`runtime-status` (необязательный)**. Очистить журнал экземпляров с определенным состоянием (например, запущенных или завершенных). Можно указать несколько состояний (через пробел).
* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)**. Имя центра задач устойчивых функций для использования. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать [host.json](durable-functions-bindings.md#host-json), с помощью durableTask:HubName.

Следующая команда удаляет журнал всех неудачных экземпляров, созданных до 14 ноября 2018 г., в 19:35:00 (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Удалить центр задач

С помощью [основных инструментов функций Azure](../functions-run-local.md) `durable delete-task-hub` команды можно удалить все хранилища артефактов, связанных с конкретной задачей концентратора. К ним относятся таблицы, очереди и большие двоичные объекты службы хранилища Azure. Команда имеет два параметра:

* **`connection-string-setting` (необязательный)**. Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)**. Имя центра задач устойчивых функций для использования. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать [host.json](durable-functions-bindings.md#host-json), с помощью durableTask:HubName.

Следующая команда удаляет все данные хранилища Azure, связанные с `UserTest` центр задач.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> Узнайте, как использовать [HTTP-интерфейсы API для управления экземплярами](durable-functions-http-api.md)

<!-- Update_Description: wording update -->