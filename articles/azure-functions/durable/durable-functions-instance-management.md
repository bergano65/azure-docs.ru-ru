---
title: Управление экземплярами в устойчивых функциях (Azure)
description: Сведения о том, как управлять экземплярами в расширении устойчивых функций для Функций Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 3db0cd3dd01e3f5f6af6b4b668d1ccac094624a2
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735170"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Управление экземплярами в Устойчивых функциях в Azure

Если вы используете расширение [устойчивые функции](durable-functions-overview.md) для функций Azure или хотите приступить к этому, убедитесь, что вы получаете оптимальное использование. Вы можете оптимизировать Устойчивые функции экземпляры оркестрации, изучите дополнительные сведения об управлении ими. В этой статье подробно описаны все операции по управлению экземплярами.

Можно запускать и завершать экземпляры, например, а также выполнять запросы к экземплярам, в том числе возможность запрашивать все экземпляры и экземпляры запросов с фильтрами. Кроме того, можно отправить события в экземпляры, дождаться завершения оркестрации и получить URL-адреса веб-перехватчиков управления HTTP. В этой статье также рассматриваются другие операции управления, включая сброс экземпляров, очистку журнала экземпляра и удаление центра задач.

В Устойчивые функции есть варианты реализации каждой из этих операций управления. В этой статье приведены примеры использования [Azure functions Core Tools](../functions-run-local.md) для .NET (C#) и JavaScript.

## <a name="start-instances"></a>Запуск экземпляров

Важно иметь возможность запустить экземпляр оркестрации. Обычно это делается при использовании привязки Устойчивые функции в триггере другой функции.

Метод [стартневасинк](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) в [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) `startNew` `DurableOrchestrationClient` или (JavaScript) запускает новый экземпляр. Экземпляры этого класса запрашиваются с помощью `orchestrationClient` привязки. На внутреннем уровне этот метод ставит в очередь управления сообщение, которое инициирует запуск функции с заданным именем и привязкой к триггеру `orchestrationTrigger`.

Эта асинхронная операция завершается после успешного планирования процесса оркестрации. Процесс оркестрации должен запуститься в течение 30 секунд. Если он занимает больше времени, вы `TimeoutException`увидите.

> [!WARNING]
> При локальной разработке в `WEBSITE_HOSTNAME` JavaScript задайте для переменной `localhost:<port>` среды значение (например,), `localhost:7071`чтобы использовать методы в. `DurableOrchestrationClient` Дополнительные сведения об этом требовании см. в [описании проблемы на сайте GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) принимает следующие параметры:

* **Name**. Имя функции оркестратора, для которой назначается запуск.
* **Входные данные** Любые данные, сериализуемые в формат JSON, которые нужно передать в функцию оркестратора как входные данные.
* **InstanceId**. Уникальный идентификатор экземпляра (необязательно). Если этот параметр не указан, метод использует случайный идентификатор.

Далее приводятся некоторые примеры.

### <a name="c"></a>C#

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
* **InstanceId**. Уникальный идентификатор экземпляра (необязательно). Если этот параметр не указан, метод использует случайный идентификатор.
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
> Используйте случайный идентификатор экземпляра. Это помогает обеспечить равномерное распределение нагрузки при масштабировании функций Orchestrator на нескольких виртуальных машинах. Правильное время на использование идентификаторов экземпляров, которые не являются случайными, заключается в том, что идентификатор должен поступать из внешнего источника или при реализации [одноэлементного шаблона Orchestrator](durable-functions-singletons.md) .

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Кроме того, экземпляр можно запустить непосредственно с помощью команды [Azure functions Core Tools](../functions-run-local.md) `durable start-new` . Она принимает следующие параметры.

* **`function-name` (обязательный)** . Имя запускаемой функции.
* **`input` (необязательный)** . Входные данные функции, встроенные или с помощью JSON-файла. Для файлов добавьте префикс в путь к файлу `@`, `@path/to/file.json`например.
* **`id` (необязательный)** . Идентификатор экземпляра оркестрации. Если этот параметр не указан, команда использует случайный идентификатор GUID.
* **`connection-string-setting` (необязательный)** . Имя параметра приложения, содержащего нужную строку подключения к хранилищу. По умолчанию используется AzureWebJobsStorage.
* **`task-hub-name` (необязательный)** . Имя используемого центра задач Устойчивые функции. По умолчанию используется DurableFunctionsHub. Его также можно задать в [Host. JSON](durable-functions-bindings.md#host-json) с помощью DurableTask: HubName.

> [!NOTE]
> Команды основных средств предполагают, что вы используете их из корневого каталога приложения-функции. При явном предоставлении `connection-string-setting` параметров и `task-hub-name` можно выполнять команды из любого каталога. Несмотря на то, что эти команды можно выполнять без работающего узла приложения-функции, может оказаться, что вы не можете наблюдать некоторые последствия, если только узел не запущен. Например, `start-new` команда помещает начальное сообщение в целевой центр задач, но согласование не выполняется, если не запущен процесс размещения приложения-функции, который может обработать сообщение.

Следующая команда запускает функцию HelloWorld и передает ей содержимое файла `counter-data.json` :

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Экземпляры запросов

В рамках усилий по управлению взаимодействием, скорее всего, потребуется собрать сведения о состоянии экземпляра оркестрации (например, о том, завершился ли он нормально или неудачно).

Метод [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) из класса [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) или метод `getStatus` из класса `DurableOrchestrationClient` (JavaScript) подает запрос на сведения о состоянии экземпляра оркестрации.

Он принимает параметры `instanceId` (обязательный), `showHistory` (необязательный), `showHistoryOutput` (необязательный) и `showInput` (необязательный, только для .NET).

* **`showHistory`** . Если задано `true`значение, ответ содержит журнал выполнения.
* **`showHistoryOutput`** . Если задано `true`значение, то журнал выполнения содержит выходные данные действия.
* **`showInput`** . Если задано `false`значение, ответ не будет содержать входные данные функции. Значение по умолчанию — `true`. (Только для .NET.)

Метод возвращает объект JSON со следующими свойствами:

* **Name**. Имя функции оркестратора.
* **InstanceId**. Идентификатор экземпляра оркестрации (будет совпадать с переданным значением `instanceId`).
* **CreatedTime**. Время создания, то есть время начала выполнения функции оркестратора.
* **LastUpdatedTime**. Время прохождения последней контрольной точки оркестрации.
* **Входные данные** Входные данные функции в виде значения JSON. Это поле не заполняется, `showInput` если имеет значение false.
* **CustomStatus**. Настраиваемое значение состояния оркестрации в формате JSON.
* **Выходные данные**: Выходные данные функции в формате JSON (если выполнение функции успешно завершено). Если не удалось выполнить функцию Orchestrator, это свойство содержит сведения об ошибке. Если функция Orchestrator была прервана, это свойство включает причину завершения (если есть).
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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Также можно напрямую получить состояние экземпляра оркестрации с помощью команды [Azure functions Core Tools](../functions-run-local.md) `durable get-runtime-status` . Она принимает следующие параметры.

* **`id` (обязательный)** . Идентификатор экземпляра оркестрации.
* **`show-input` (необязательный)** . Если задано `true`значение, ответ содержит входные данные функции. Значение по умолчанию — `false`.
* **`show-output` (необязательный)** . Если задано `true`значение, ответ содержит выходные данные функции. Значение по умолчанию — `false`.
* **`connection-string-setting` (необязательный)** . Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)** . Имя используемого центра задач Устойчивые функции. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать в [Host. JSON](durable-functions-bindings.md#host-json)с помощью DurableTask: HubName.

Следующая команда извлекает состояние (включая входные и выходные данные) экземпляра с ИДЕНТИФИКАТОРом экземпляра оркестрации 0ab8c55a66644d68a3a8b220b12d209c. Предполагается, что `func` команда выполняется из корневого каталога приложения функции:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Для получения журнала экземпляра `durable get-history` оркестрации можно использовать команду. Она принимает следующие параметры.

* **`id` (обязательный)** . Идентификатор экземпляра оркестрации.
* **`connection-string-setting` (необязательный)** . Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)** . Имя используемого центра задач Устойчивые функции. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать в Host. JSON с помощью durableTask: HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Запрос всех экземпляров

Вместо того, чтобы запрашивать один экземпляр в согласовании одновременно, может оказаться, что он более эффективен для выполнения запросов сразу.

С помощью метода `GetStatusAsync` (.NET) или `getStatusAll` (JavaScript) вы можете запросить данные о состоянии всех экземпляров оркестрации. В .NET можно передать `CancellationToken` объект, если вы хотите отменить его. Метод возвращает объекты с теми же свойствами, что и метод `GetStatusAsync` с параметрами.

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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Кроме того, можно запрашивать экземпляры напрямую с помощью команды [Azure functions Core Tools](../functions-run-local.md) `durable get-instances` . Она принимает следующие параметры.

* **`top` (необязательный)** . Эта команда поддерживает разбиение на страницы. Этот параметр соответствует количеству экземпляров, получаемых по запросу. По умолчанию задано значение 10.
* **`continuation-token` (необязательный)** . Токен, указывающий, какую страницу или раздел экземпляров нужно извлечь. Каждое выполнение `get-instances` возвращает маркер для извлечения следующего набора экземпляров.
* **`connection-string-setting` (необязательный)** . Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)** . Имя используемого центра задач Устойчивые функции. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать в [Host. JSON](durable-functions-bindings.md#host-json)с помощью DurableTask: HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Экземпляры запросов с фильтрами

Что делать, если вам не нужна вся информация, которую может предоставить запрос стандартного экземпляра? Например, что делать, если вы просто ищете время создания оркестрации или состояние среды выполнения оркестрации? Вы можете ограничивать запрос, применяя фильтры.

`getStatusBy` Используйте метод `GetStatusAsync` (.NET) или (JavaScript) для получения списка экземпляров оркестрации, соответствующих набору предопределенных фильтров.

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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

В Azure functions Core Tools можно также использовать `durable get-instances` команду с фильтрами. В дополнение `top`к вышеупомянутым параметрам `continuation-token` `connection-string-setting`,, и `task-hub-name` можно использовать три параметра фильтра (`created-after`, `created-before`и `runtime-status`).

* **`created-after` (необязательный)** . Получение экземпляров, созданных после этой даты и времени в формате UTC. Принимаются значения даты и времени в формате ISO 8601.
* **`created-before` (необязательный)** . Получение экземпляров, созданных до этой даты и времени в формате UTC. Принимаются значения даты и времени в формате ISO 8601.
* **`runtime-status` (необязательный)** . Получите экземпляры с определенным состоянием (например, "выполняется" или "завершено"). Можно указать несколько состояний (через пробел).
* **`top` (необязательный)** . Количество экземпляров, получаемых по запросу. По умолчанию задано значение 10.
* **`continuation-token` (необязательный)** . Токен, указывающий, какую страницу или раздел экземпляров нужно извлечь. Каждое выполнение `get-instances` возвращает маркер для извлечения следующего набора экземпляров.
* **`connection-string-setting` (необязательный)** . Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)** . Имя используемого центра задач Устойчивые функции. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать в [Host. JSON](durable-functions-bindings.md#host-json)с помощью DurableTask: HubName.

Если не указать фильтры`created-after`(, `created-before`или `runtime-status`), команда просто извлекает `top` экземпляры, не обращаясь в состояние выполнения или время создания.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Завершить экземпляры

Если у вас есть экземпляр оркестрации, выполнение которого занимает слишком много времени, или необходимо остановить его до завершения по какой-либо причине, у вас есть возможность завершить его.

Можно использовать метод [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) класса [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) или `terminate` метод `DurableOrchestrationClient` класса (JavaScript). Эти два параметра представляют `instanceId` `reason` собой строку, которая записывается в журналы и в состояние экземпляра. Прекращенный экземпляр останавливается, как только он достигнет `await` следующей (.NET) или `yield` (JavaScript) точки или завершается немедленно, если уже находится в `await` или `yield`.

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
> Прекращение работы экземпляра в настоящее время не распространяется. Функции действий и взаимосвязи выполняются до завершения независимо от того, был ли завершен экземпляр оркестрации, вызвавший их.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Кроме того, экземпляр оркестрации можно завершить напрямую, используя команду [Azure functions Core Tools](../functions-run-local.md) `durable terminate` . Она принимает следующие параметры.

* **`id` (обязательный)** . Идентификатор экземпляра оркестрации для завершения.
* **`reason` (необязательный)** . Причина завершения.
* **`connection-string-setting` (необязательный)** . Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)** . Имя используемого центра задач Устойчивые функции. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать в [Host. JSON](durable-functions-bindings.md#host-json)с помощью DurableTask: HubName.

Следующая команда завершает экземпляр оркестрации с ИДЕНТИФИКАТОРом 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Отправка событий в экземпляры

В некоторых случаях очень важно, чтобы функции Orchestrator могли ожидать и прослушивать внешние события. Сюда входят [функции](durable-functions-concepts.md#monitoring) и функции мониторинга, ожидающие [взаимодействия человека](durable-functions-concepts.md#human).

Отправка уведомлений о событиях в выполняемые экземпляры с помощью метода [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) класса [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET `raiseEvent` ) или метода `DurableOrchestrationClient` класса (JavaScript). Чтобы обрабатывать эти уведомления, экземпляр должен ожидать вызова при помощи функции [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) или `waitForExternalEvent` (JavaScript).

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
> Если экземпляр оркестрации с указанным ИДЕНТИФИКАТОРом экземпляра отсутствует или если экземпляр не ожидает указанного имени события, сообщение о событии отбрасывается. Дополнительные сведения об этом поведении см. в [описании проблемы на GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Можно также вызвать событие непосредственно в экземпляре оркестрации с помощью команды [Azure functions Core Tools](../functions-run-local.md) `durable raise-event` . Она принимает следующие параметры.

* **`id` (обязательный)** . Идентификатор экземпляра оркестрации.
* **`event-name` (необязательный)** . Имя порождаемого события. Значение по умолчанию — `$"Event_{RandomGUID}"`.
* **`event-data` (необязательный)** . Данные, отправляемые в экземпляр оркестрации. Это может быть путь к JSON-файлу или данные, которые можно указать непосредственно в командной строке.
* **`connection-string-setting` (необязательный)** . Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)** . Имя используемого центра задач Устойчивые функции. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать в [Host. JSON](durable-functions-bindings.md#host-json)с помощью DurableTask: HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Дождитесь, пока завершится оркестрация.

В долгосрочных согласованиях может потребоваться подождать и получить результаты оркестрации. В таких случаях также полезно иметь возможность определить период времени ожидания для оркестрации. При превышении времени ожидания возвращается состояние оркестрации, а не результаты.

Класс [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) предоставляет API [ваитфоркомплетионоркреатечеккстатусреспонсеасинк](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) в .NET. Этот API можно использовать для синхронного получения фактических выходных данных из экземпляра оркестрации. В JavaScript класс `DurableOrchestrationClient` предоставляет API-интерфейс `waitForCompletionOrCreateCheckStatusResponse` для той же цели. Если они не заданы, методы используют значение по умолчанию `timeout`, равное 10 секундам, и 1 секунда для. `retryInterval`  

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
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
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
> В зависимости от используемой версии узла функций Azure может отличаться формат URL-адресов веб перехватчиков. Описанный выше пример предназначен для узла Функций Azure 2.x.

## <a name="retrieve-http-management-webhook-urls"></a>Получение URL-адресов веб-перехватчика управления HTTP

Внешнюю систему можно использовать для отслеживания или вызова событий в оркестрации. Внешние системы могут обмениваться данными с Устойчивые функции через URL-адреса перехватчика, которые являются частью ответа по умолчанию, описанного в разделе [обнаружение URL-адресов API HTTP](durable-functions-http-api.md). Однако веб-перехватчики также доступны программно в клиенте оркестрации или в функции действия. Для этого используется метод [креатехттпманажементпайлоад](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) класса [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) или `createHttpManagementPayload` метод `DurableOrchestrationClient` класса (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) и `createHttpManagementPayload` имеют один параметр.

* **instanceId**. Уникальный идентификатор экземпляра.

Методы возвращают экземпляр [хттпманажементпайлоад](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) или объект (JavaScript) со следующими строковыми свойствами:

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

## <a name="rewind-instances-preview"></a>Перемотка экземпляров (Предварительная версия)

При возникновении сбоя оркестрации в случае непредвиденной причины можно *Перемотать* экземпляр на предыдущее работоспособное состояние, используя API, созданный для этой цели.

> [!NOTE]
> Этот API не является заменой правильной политики повторов и обработки ошибок. Вместо этого его следует применять исключительно в тех случаях, когда причини сбоя экземпляра оркестрации неизвестные. Дополнительные сведения об обработке ошибок и политиках повтора см. в разделе [Обработка ошибок](durable-functions-error-handling.md) .

Используйте API [ревиндасинк](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) или `rewindAsync` (JavaScript), чтобы перевести согласование обратно в состояние *выполнения* . Повторный запуск действия или сбоев при взаимодействии, вызвавших сбой оркестрации.

Например, предположим, что у вас есть рабочий процесс, включающий серию [утверждений человека](durable-functions-concepts.md#human). Предположим, что существует ряд функций действий, которые уведомляют пользователей о необходимости их утверждения и ожидают ответа в режиме реального времени. После того как все действия по утверждению получили ответы или истекло время ожидания, предположим, что другое действие завершается сбоем из-за неправильной настройки приложения, например неверной строки подключения к базе данных. Как результат — сбой оркестрации в рабочем процессе. С помощью API `rewindAsync` -интерфейса(.NET)или(JavaScript)Администраторприложенияможетисправитьошибкуконфигурациииперемотатьнеудачноесогласованиеобратновсостояние`RewindAsync` непосредственно перед сбоем. Ни один из действий человека не требует повторного утверждения, и теперь согласование может завершиться успешно.

> [!NOTE]
> Функция *перемотки назад* не поддерживает Перемотка экземпляров оркестрации, использующих устойчивые таймеры.

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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Можно также перемотать экземпляр оркестрации напрямую с помощью команды [Azure functions Core Tools](../functions-run-local.md) `durable rewind` . Она принимает следующие параметры.

* **`id` (обязательный)** . Идентификатор экземпляра оркестрации.
* **`reason` (необязательный)** . Причина перемотки экземпляра оркестрации.
* **`connection-string-setting` (необязательный)** . Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)** . Имя используемого центра задач Устойчивые функции. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать в [Host. JSON](durable-functions-bindings.md#host-json)с помощью DurableTask: HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Очистить журнал экземпляров

Чтобы удалить все данные, связанные с согласованием, можно очистить журнал экземпляров. Например, может потребоваться удалить строки таблицы Azure и большие двоичные объекты сообщений, если они существуют. Для этого используйте API [пуржеинстанцехисторясинк](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) .

> [!NOTE]
> API `PurgeInstanceHistoryAsync` в настоящее время доступен только для C#.

 У метода есть две перегрузки. Первый удаляет журнал по ИДЕНТИФИКАТОРу экземпляра оркестрации:

### <a name="c"></a>C#

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

Во втором примере показана активируемая по таймеру функция, которая удаляет из журнала все экземпляры оркестрации, выполненные после указанного интервала времени. В этом случае он удаляет данные для всех экземпляров, выполненных 30 или более дней назад. Оно запускается раз в день, в 00:00:

### <a name="c"></a>C#

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
> Для успешного завершения процесса функции, запускаемого по времени, состояние среды выполнения должно быть " **завершено** **", "завершено**" или " **сбой**".

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Журнал экземпляра оркестрации можно очистить с помощью команды [Azure functions Core Tools](../functions-run-local.md) `durable purge-history` . Аналогично второму C# примеру в предыдущем разделе, он очищает журнал для всех экземпляров оркестрации, созданных в течение заданного интервала времени. Удаленные экземпляры можно фильтровать по состоянию выполнения. Команда имеет несколько параметров:

* **`created-after` (необязательный)** . Удаление из журнала экземпляров, созданных после этой даты и времени в формате UTC. Принимаются значения даты и времени в формате ISO 8601.
* **`created-before` (необязательный)** . Удаление из журнала экземпляров, созданных до этой даты и времени в формате UTC. Принимаются значения даты и времени в формате ISO 8601.
* **`runtime-status` (необязательный)** . Очистка журнала экземпляров с определенным состоянием (например, "выполняется" или "завершено"). Можно указать несколько состояний (через пробел).
* **`connection-string-setting` (необязательный)** . Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)** . Имя используемого центра задач Устойчивые функции. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать в [Host. JSON](durable-functions-bindings.md#host-json)с помощью DurableTask: HubName.

Следующая команда удаляет журнал всех неудачных экземпляров, созданных до 14 ноября 2018 в 7:35 по Гринвичу (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Удаление центра задач

С помощью команды [Azure functions Core Tools](../functions-run-local.md) `durable delete-task-hub` можно удалить все артефакты хранилища, связанные с определенным центром задач. К ним относятся таблицы, очереди и большие двоичные объекты службы хранилища Azure. Команда имеет два параметра:

* **`connection-string-setting` (необязательный)** . Имя параметра приложения, содержащего нужную строку подключения к хранилищу. Значение по умолчанию — `AzureWebJobsStorage`.
* **`task-hub-name` (необязательный)** . Имя используемого центра задач Устойчивые функции. Значение по умолчанию — `DurableFunctionsHub`. Его также можно задать в [Host. JSON](durable-functions-bindings.md#host-json)с помощью DurableTask: HubName.

Следующая команда удаляет все данные службы хранилища Azure, `UserTest` связанные с концентратором задач.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> Узнайте, как использовать [HTTP-интерфейсы API для управления экземплярами](durable-functions-http-api.md)