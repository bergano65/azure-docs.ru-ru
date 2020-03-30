---
title: Управление экземплярами в устойчивых функциях (Azure)
description: Сведения о том, как управлять экземплярами в расширении устойчивых функций для Функций Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 07a96fdd6350d8db38a92c23e510afb05f7416fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277756"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Управление экземплярами в Устойчивых функциях в Azure

Если вы используете расширение [«Долговечные функции»](durable-functions-overview.md) для функций Azure или хотите начать это делать, убедитесь, что вы получаете лучшее использование из него. Можно оптимизировать экземпляры оркестровки функций длительного пользования, узнав больше о том, как ими управлять. В этой статье подробно описаны все операции по управлению экземплярами.

Например, можно запускать и прекращать экземпляры, и можно запросить экземпляры, включая возможность запроса всех экземпляров и экземпляров запросов с помощью фильтров. Кроме того, можно отправлять события в экземпляры, ждать завершения согласования и получать URL-адреса webhook управления HTTP. В этой статье также рассматриваются другие операции управления, включая перемотка экземпляров, очистку истории экземпляров и удаление концентратора задач.

В функционал ных работах у вас есть варианты того, как реализовать каждую из этих операций управления. В этой статье приводятся примеры, в которые используются [основные инструменты Azure Functions](../functions-run-local.md) для .NET (C) и JavaScript.

## <a name="start-instances"></a>Запуск экземпляров

Важно иметь возможность начать экземпляр оркестровки. Обычно это делается, когда вы используете связывание прочных функций в триггере другой функции.

Метод `StartNewAsync` (.NET) `startNew` или (JavaScript) на [связывании клиента оркестровки](durable-functions-bindings.md#orchestration-client) запускает новый экземпляр. Внутренне этот метод закрепляет сообщение в очередь управления, что затем запускает начало функции с указанным именем, использующее [связывание триггера оркестровки.](durable-functions-bindings.md#orchestration-trigger)

Эта асинхронная операция завершается после успешного планирования процесса оркестрации.

Параметры для запуска нового экземпляра оркестровки следующие:

* **Name.** Имя функции оркестратора, для которой назначается запуск.
* **Input.** Любые данные, сериализуемые в формат JSON, которые нужно передать в функцию оркестратора как входные данные.
* **InstanceId.** Уникальный идентификатор экземпляра (необязательно). Если вы не указали этот параметр, метод использует случайный идентификатор.

> [!TIP]
> Используйте случайный идентификатор экземпляра. Иди случайных экземпляров помогает обеспечить равное распределение нагрузки при масштабировании функций оркестратора на нескольких ВМ. Подходящее время для использования неслучайных идентификаторов экземпляров — это когда идентификатор должен исходить от внешнего источника или когда вы реализуете шаблон [оркестратора синглтона.](durable-functions-singletons.md)

Следующий код — это примерная функция, которая запускает новый экземпляр оркестровки:

# <a name="c"></a>[C #](#tab/csharp)

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
> Предыдущий код C-кода предназначен для функций длительного пользования 2.x. Для функций Durable 1.x `OrchestrationClient` необходимо использовать `DurableClient` атрибут вместо атрибута, а `IDurableOrchestrationClient`вместо этого — тип `DurableOrchestrationClient` параметра. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[Javascript](#tab/javascript)

<a name="javascript-function-json"></a>Если не указано иное, примеры на этой странице используют триггер HTTP со следующим function.json.

**function.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
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

> [!NOTE]
> Этот пример нацелен на версию Durable Functions 2.x. В версии 1.x `orchestrationClient` `durableClient`используйте вместо .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Вы также можете запустить экземпляр непосредственно с помощью команды `durable start-new` [Основных инструментов функций Azure.](../functions-run-local.md) Она принимает следующие параметры.

* (требуется) : Название функции для начала. ** `function-name` **
* (необязательно) : Ввод функции, либо в строке или через файл JSON. ** `input` ** Для файлов добавьте префикс к пути `@`к `@path/to/file.json`файлу, например.
* (необязательно) : Идентификатор экземпляра оркестровки. ** `id` ** Если вы не указали этот параметр, команда использует случайный GUID.
* (необязательно) : Название параметра приложения, содержащего строку подключения к хранилищу для использования. ** `connection-string-setting` ** По умолчанию используется AzureWebJobsStorage.
* (необязательно) : Название концентратора задач «Долговечные функции» для использования. ** `task-hub-name` ** По умолчанию используется DurableFunctionsHub. Вы также можете установить это в [host.json](durable-functions-bindings.md#host-json) с помощью прочнаяTask:HubName.

> [!NOTE]
> Команды Core Tools предполагают, что вы управляете ими из корневого каталога приложения функции. Если вы явно `connection-string-setting` укажите параметры и `task-hub-name` параметры, вы можете запустить команды из любого каталога. Хотя вы можете запускать эти команды без запуска хоста функционального приложения, вы можете обнаружить, что вы не можете наблюдать некоторые эффекты, если хост не работает. Например, `start-new` команда заготавлив амбу запуска в концентратор целевой задачи, но оркестровка фактически не заражается, если не будет запущен процесс узла функционального приложения, который может обрабатывать сообщение.

Следующая команда запускает функцию под названием HelloWorld `counter-data.json` и передает содержимое файла ей:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Экземпляры запросов

В рамках ваших усилий по управлению оркестровками вам, скорее всего, потребуется собрать информацию о состоянии экземпляра оркестровки (например, была ли она выполнена нормально или не выполнена).

Метод `GetStatusAsync` (.NET) `getStatus` или (JavaScript) на [связывающем клиенте-оркестре](durable-functions-bindings.md#orchestration-client) запрашивает состояние экземпляра оркестровки.

Он принимает параметры `instanceId` (обязательный), `showHistory` (необязательный), `showHistoryOutput` (необязательный) и `showInput` (необязательный).

* **`showHistory`**: Если `true`установлен, ответ содержит историю выполнения.
* **`showHistoryOutput`**: Если `true`установлено, история выполнения содержит выводы активности.
* **`showInput`**: Если `false`установить, ответ не будет содержать входную функцию. Значение по умолчанию — `true`.

Этот метод возвращает объект со следующими свойствами:

* **Name.** Имя функции оркестратора.
* **InstanceId.** Идентификатор экземпляра оркестрации (будет совпадать с переданным значением `instanceId`).
* **CreatedTime.** Время создания, то есть время начала выполнения функции оркестратора.
* **LastUpdatedTime.** Время прохождения последней контрольной точки оркестрации.
* **Input.** Входные данные функции в формате JSON. Это поле не заселены, если `showInput` является ложным.
* **CustomStatus.** Настраиваемое значение состояния оркестрации в формате JSON.
* **Output.** Выходные данные функции в формате JSON (если выполнение функции успешно завершено). Если функция оркестратора не сработала, это свойство включает детали сбоя. Если функция оркестратора была прекращена, это свойство включает в себя причину прекращения (если таковая имеется).
* **RuntimeStatus.** Состояние выполнения может иметь одно из следующих значений:
  * **Pending**. Выполнение экземпляра было запланировано, но еще не начато.
  * **Running.** Началось выполнение экземпляра.
  * **Completed.** Выполнение экземпляра завершилось в обычном режиме.
  * **ContinuedAsNew.** Экземпляр выполнил перезапуск с очисткой журнала выполнения. Это состояние является переходным состоянием.
  * **Failed.** Выполнение экземпляра завершилось ошибкой.
  * **Terminated**: выполнение экземпляра было прервано.
* **History**: журнал выполнения оркестрации. Это поле заполняется, только если `showHistory` имеет значение `true`.

Этот метод `null` возвращается (.NET) или `undefined` (JavaScript), если экземпляр не существует.

# <a name="c"></a>[C #](#tab/csharp)

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
> Предыдущий код C-кода предназначен для функций длительного пользования 2.x. Для функций Durable 1.x `OrchestrationClient` необходимо использовать `DurableClient` атрибут вместо атрибута, а `IDurableOrchestrationClient`вместо этого — тип `DurableOrchestrationClient` параметра. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

Смотрите [примеры Start](#javascript-function-json) для конфигурации function.json.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Также можно получить статус экземпляра оркестровки непосредственно с помощью команды `durable get-runtime-status` [«Основные инструменты функций Azure».](../functions-run-local.md) Она принимает следующие параметры.

* (требуется) : Идентификатор экземпляра оркестровки. ** `id` **
* (необязательно) : `true`Если установлено, ответ содержит вход ную функцию. ** `show-input` ** Значение по умолчанию — `false`.
* (необязательно) : `true`Если установлено, ответ содержит выход функции. ** `show-output` ** Значение по умолчанию — `false`.
* (необязательно) : Название параметра приложения, содержащего строку подключения к хранилищу для использования. ** `connection-string-setting` ** Значение по умолчанию — `AzureWebJobsStorage`.
* (необязательно) : Название концентратора задач «Долговечные функции» для использования. ** `task-hub-name` ** Значение по умолчанию — `DurableFunctionsHub`. Он также может быть установлен в [host.json](durable-functions-bindings.md#host-json), с помощью прочногоTask:HubName.

Следующая команда получает статус (включая вход и выход) экземпляра с идентификатором экземпляра оркестровки 0ab8c55a6664d664d68a8a8b220b12d209c. Он предполагает, что вы `func` работаете команды из корневого каталога приложения функции:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Вы можете `durable get-history` использовать команду для получения истории экземпляра оркестровки. Она принимает следующие параметры.

* (требуется) : Идентификатор экземпляра оркестровки. ** `id` **
* (необязательно) : Название параметра приложения, содержащего строку подключения к хранилищу для использования. ** `connection-string-setting` ** Значение по умолчанию — `AzureWebJobsStorage`.
* (необязательно) : Название концентратора задач «Долговечные функции» для использования. ** `task-hub-name` ** Значение по умолчанию — `DurableFunctionsHub`. Он также может быть установлен в host.json, используя прочнуюзадачу:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Запрос всех экземпляров

Вместо того, чтобы загонять один экземпляр в оркестровке за раз, вы можете найти более эффективным запрос все из них одновременно.

С помощью метода `GetStatusAsync` (.NET) или `getStatusAll` (JavaScript) вы можете запросить данные о состоянии всех экземпляров оркестрации. В .NET вы можете `CancellationToken` передать объект в случае, если вы хотите его отменить. Метод возвращает объекты с теми же свойствами, что и метод `GetStatusAsync` с параметрами.

# <a name="c"></a>[C #](#tab/csharp)

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
> Предыдущий код C-кода предназначен для функций длительного пользования 2.x. Для функций Durable 1.x `OrchestrationClient` необходимо использовать `DurableClient` атрибут вместо атрибута, а `IDurableOrchestrationClient`вместо этого — тип `DurableOrchestrationClient` параметра. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Смотрите [примеры Start](#javascript-function-json) для конфигурации function.json.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Кроме того, можно заставить зазапросы напрямую, используя команду `durable get-instances` [Основных инструментов функций Azure.](../functions-run-local.md) Она принимает следующие параметры.

* (необязательно) : Эта команда поддерживает paging. ** `top` ** Этот параметр соответствует количеству экземпляров, получаемых по запросу. Значение по умолчанию равно 10.
* (необязательно): токен, указывающий, какую страницу или раздел экземпляров следует получить. ** `continuation-token` ** Каждое выполнение `get-instances` возвращает маркер для извлечения следующего набора экземпляров.
* (необязательно) : Название параметра приложения, содержащего строку подключения к хранилищу для использования. ** `connection-string-setting` ** Значение по умолчанию — `AzureWebJobsStorage`.
* (необязательно) : Название концентратора задач «Долговечные функции» для использования. ** `task-hub-name` ** Значение по умолчанию — `DurableFunctionsHub`. Он также может быть установлен в [host.json](durable-functions-bindings.md#host-json), с помощью прочногоTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Экземпляры запросов с фильтрами

Что делать, если вам не нужна вся информация, которую может предоставить стандартный запрос экземпляра? Например, что делать, если вы просто ищете время создания оркестровки или статус запуска времени выполнения? Можно сузить запрос, применяя фильтры.

Используйте `GetStatusAsync` метод (.NET) или `getStatusBy` (JavaScript), чтобы получить список экземпляров оркестровки, которые соответствуют набору предопределенных фильтров.

# <a name="c"></a>[C #](#tab/csharp)

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
> Предыдущий код C-кода предназначен для функций длительного пользования 2.x. Для функций Durable 1.x `OrchestrationClient` необходимо использовать `DurableClient` атрибут вместо атрибута, а `IDurableOrchestrationClient`вместо этого — тип `DurableOrchestrationClient` параметра. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Смотрите [примеры Start](#javascript-function-json) для конфигурации function.json.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

В основных инструментах Azure Functions `durable get-instances` можно также использовать команду с фильтрами. В `top`дополнение к вышеупомянутым `continuation-token` `connection-string-setting`, `task-hub-name` , , и параметры, вы`created-after`можете `created-before`использовать `runtime-status`три параметра фильтра (, и ).

* (необязательно) : Извлеките экземпляры, созданные после этой даты/времени (UTC). ** `created-after` ** Принимаются значения даты и времени в формате ISO 8601.
* (необязательно) : Извлеките экземпляры, созданные до этой даты/времени (UTC). ** `created-before` ** Принимаются значения даты и времени в формате ISO 8601.
* (необязательно) : Извлекать экземпляры с определенным статусом (например, запущенными или завершенными). ** `runtime-status` ** Можно указать несколько состояний (через пробел).
* (необязательно) : Количество экземпляров, извлеченных за запрос. ** `top` ** Значение по умолчанию равно 10.
* (необязательно): токен, указывающий, какую страницу или раздел экземпляров следует получить. ** `continuation-token` ** Каждое выполнение `get-instances` возвращает маркер для извлечения следующего набора экземпляров.
* (необязательно) : Название параметра приложения, содержащего строку подключения к хранилищу для использования. ** `connection-string-setting` ** Значение по умолчанию — `AzureWebJobsStorage`.
* (необязательно) : Название концентратора задач «Долговечные функции» для использования. ** `task-hub-name` ** Значение по умолчанию — `DurableFunctionsHub`. Он также может быть установлен в [host.json](durable-functions-bindings.md#host-json), с помощью прочногоTask:HubName.

Если вы не предоставляете никаких фильтров`created-after`(, `created-before`или), `runtime-status`команда просто извлекает `top` экземпляры, без учета статуса времени выполнения или времени создания.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Прекращение экземпляров

Если у вас есть экземпляр оркестровки, который занимает слишком много времени, или вам просто нужно остановить его, прежде чем он завершится по какой-либо причине, у вас есть возможность прекратить его.

Для прекращения `TerminateAsync` экземпляров можно использовать `terminate` метод (.NET) или (JavaScript) [привязки клиента](durable-functions-bindings.md#orchestration-client) к оркестровке. Эти два параметра `instanceId` являются `reason` строкой и строкой, которые записываются в журналы и статус экземпляра.

# <a name="c"></a>[C #](#tab/csharp)

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
> Предыдущий код C-кода предназначен для функций длительного пользования 2.x. Для функций Durable 1.x `OrchestrationClient` необходимо использовать `DurableClient` атрибут вместо атрибута, а `IDurableOrchestrationClient`вместо этого — тип `DurableOrchestrationClient` параметра. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

Смотрите [примеры Start](#javascript-function-json) для конфигурации function.json.

---

Прекратив преобразование в `Terminated` конечном итоге переходит в состояние. Однако этот переход не произойдет сразу. Скорее, операция завершения будет стоять в очереди в концентраторе задач вместе с другими операциями для этого экземпляра. Можно использовать AIS [запроса экземпляров,](#query-instances) чтобы узнать, `Terminated` когда вырваное экземпляр фактически достигло состояния.

> [!NOTE]
> Прекращение инстанций в настоящее время не распространяется. Функции действия и подоркестрации завершаются до завершения, независимо от того, прекратили ли вы экземпляр оркестровки, который их вызывал.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Можно также напрямую прекратить экземпляр оркестровки, используя команду `durable terminate` [Основных инструментов функций Azure.](../functions-run-local.md) Она принимает следующие параметры.

* (требуется) : Идентификатор экземпляра оркестровки для завершения. ** `id` **
* (необязательно) : Причина прекращения. ** `reason` **
* (необязательно) : Название параметра приложения, содержащего строку подключения к хранилищу для использования. ** `connection-string-setting` ** Значение по умолчанию — `AzureWebJobsStorage`.
* (необязательно) : Название концентратора задач «Долговечные функции» для использования. ** `task-hub-name` ** Значение по умолчанию — `DurableFunctionsHub`. Он также может быть установлен в [host.json](durable-functions-bindings.md#host-json), с помощью прочногоTask:HubName.

Следующая команда завершает экземпляр оркестровки с идентификатором 0ab8c55a6644d68a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Отправка событий в экземпляры

В некоторых сценариях важно, чтобы функции оркестратора могли ждать и слушать внешние события. Это включает в себя [функции мониторинга](durable-functions-overview.md#monitoring) и функции, которые ждут [взаимодействия человека.](durable-functions-overview.md#human)

Отправка уведомлений о событиях `RaiseEventAsync` в запущенные экземпляры с помощью метода (.NET) или метода `raiseEvent` (JavaScript) [связывания клиента-оркестра.](durable-functions-bindings.md#orchestration-client) Случаи, которые могут обрабатывать эти события являются `WaitForExternalEvent` те, которые ожидают вызова `waitForExternalEvent` (.NET) или уступая (JavaScript) вызова.

Параметры (.NET) `RaiseEventAsync` и `raiseEvent` (JavaScript) следующие:

* **InstanceId**: Уникальный идентификатор экземпляра.
* **EventName** — имя отправляемого события;
* **EventData** — данные полезной нагрузки в формате JSON, отправляемые в экземпляр.

# <a name="c"></a>[C #](#tab/csharp)

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
> Предыдущий код C-кода предназначен для функций длительного пользования 2.x. Для функций Durable 1.x `OrchestrationClient` необходимо использовать `DurableClient` атрибут вместо атрибута, а `IDurableOrchestrationClient`вместо этого — тип `DurableOrchestrationClient` параметра. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Смотрите [примеры Start](#javascript-function-json) для конфигурации function.json.

---

> [!NOTE]
> Если экземпляр оркестрации с указанным идентификатором экземпляра отсутствует, сообщение о событии отменяется. Если экземпляр существует, но он еще не ожидает события, событие будет храниться в состоянии экземпляра до тех пор, пока оно не будет готово к получению и обработке.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Вы также можете довести событие до экземпляра оркестровки непосредственно с помощью команды `durable raise-event` [«Основные инструменты функций Azure».](../functions-run-local.md) Она принимает следующие параметры.

* (требуется) : Идентификатор экземпляра оркестровки. ** `id` **
* **`event-name`**: Название мероприятия, чтобы поднять.
* (необязательно) : Данные для отправки в экземпляр оркестровки. ** `event-data` ** Это может быть путь к файлу JSON, или вы можете предоставить данные непосредственно на командной строке.
* (необязательно) : Название параметра приложения, содержащего строку подключения к хранилищу для использования. ** `connection-string-setting` ** Значение по умолчанию — `AzureWebJobsStorage`.
* (необязательно) : Название концентратора задач «Долговечные функции» для использования. ** `task-hub-name` ** Значение по умолчанию — `DurableFunctionsHub`. Он также может быть установлен в [host.json](durable-functions-bindings.md#host-json), с помощью прочногоTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Дождитесь, пока завершится оркестрация.

В длительных оркестровок, вы можете подождать и получить результаты оркестровки. В этих случаях также полезно определить период тайм-аута в оркестровке. Если тайм-аут превышен, состояние оркестровки должно быть возвращено вместо результатов.

Метод `WaitForCompletionOrCreateCheckStatusResponseAsync` (.NET) `waitForCompletionOrCreateCheckStatusResponse` или (JavaScript) может быть использован для синхронного получения фактического вывода из экземпляра оркестровки. По умолчанию эти методы используют значение по `timeout`умолчанию 10 секунд для, и 1 секунда для. `retryInterval`  

Ниже приведен пример функции HTTP-триггера, в котором показано, как использовать этот API:

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Смотрите [примеры Start](#javascript-function-json) для конфигурации function.json.

---

Вызов функции со следующей строкой. Используйте 2 секунды для тайм-аута и 0,5 секунды для интервала повтора:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

В зависимости от того, сколько времени уйдет на получение ответа от экземпляра оркестрации, возможны два варианта развития событий.

* Случаи оркестровки завершены в пределах определенного тайм-аута (в данном случае 2 секунды), а ответ — это фактический выход экземпляра оркестровки, поставленный синхронно:

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

* Случаи оркестровки не могут быть завершены в пределах определенного тайм-аута, а ответ по умолчанию описан в [открытии URL-адресов HTTP API:](durable-functions-http-api.md)

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
> Формат URL-адресов webhook может отличаться в зависимости от того, какую версию хоста Azure Functions вы выполняете. Описанный выше пример предназначен для узла Функций Azure 2.0.

## <a name="retrieve-http-management-webhook-urls"></a>Retrieve HTTP управления WEBhook URL-адреса

Можно использовать внешнюю систему для мониторинга или дознания событий до оркестровки. Внешние системы могут общаться с долгосрочными функциями через URL-адреса webhook, которые являются частью ответа по умолчанию, описанного в [открытии URL-адреса HTTP API.](durable-functions-http-features.md#http-api-url-discovery) URL-адреса webhook могут быть доступны программно с использованием [связывания клиента оркестровки.](durable-functions-bindings.md#orchestration-client) Методы `CreateHttpManagementPayload` (.NET) `createHttpManagementPayload` или (JavaScript) могут быть использованы для получения сериализируемого объекта, содержащего эти URL-адреса webhook.

Методы `CreateHttpManagementPayload` (.NET) и `createHttpManagementPayload` (JavaScript) имеют один параметр:

* **InstanceId** — уникальный идентификатор экземпляра.

Методы возвращают объект со следующими свойствами строки:

* **Id** — идентификатор экземпляра оркестрации (будет совпадать с переданным значением `InstanceId`).
* **StatusQueryGetUri** — URL-адрес состояния экземпляра оркестрации.
* **SendEventPostUri** — URL-адрес вызова события экземпляра оркестрации.
* **TerminatePostUri** — URL-адрес завершения работы экземпляра оркестрации.
* **ОчисткаИсторияУдалитьУри**: "История чистки" URL-адреса экземпляра оркестровки.

Функции могут отправлять экземпляры этих объектов внешним системам для мониторинга или поднятия событий на соответствующих оркестровках, как показано в следующих примерах:

# <a name="c"></a>[C #](#tab/csharp)

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
> Предыдущий код C-кода предназначен для функций длительного пользования 2.x. Для функций Durable 1.x `DurableActivityContext` необходимо `IDurableActivityContext`использовать вместо `OrchestrationClient` этого атрибут `DurableClient` вместо атрибута, а `DurableOrchestrationClient` тип параметра — `IDurableOrchestrationClient`— это не сделать. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Смотрите [примеры Start](#javascript-function-json) для конфигурации function.json.

---

## <a name="rewind-instances-preview"></a>Перемотка экземпляров (предварительный просмотр)

Если по неожиданной причине произошел сбой сработка оркестровки, можно *перемотать* экземпляр в ранее здоровое состояние, используя API, построенный для этой цели.

> [!NOTE]
> Этот API не является заменой правильной политики повторов и обработки ошибок. Вместо этого его следует применять исключительно в тех случаях, когда причини сбоя экземпляра оркестрации неизвестные. Для получения дополнительной информации о политике обработки ошибок и повторной попытки см. [Error handling](durable-functions-error-handling.md)

Используйте `RewindAsync` (.NET) `rewind` или (JavaScript) метод [связывания клиента оркестровки,](durable-functions-bindings.md#orchestration-client) чтобы вернуть оркестровку в состояние *Running.* Этот метод также будет повторно запускать действия или сбои выполнения суб-оркестра, которые вызвали сбой оркестровки.

Например, предположим, что у вас есть рабочий процесс, включающий ряд [человеческих утверждений.](durable-functions-overview.md#human) Предположим, что существует ряд функций активности, которые уведомляют кого-то о необходимости их утверждения и переивают ответ в режиме реального времени. После того, как все действия по утверждению получили ответы или приурочены, предположим, что другое действие не удается из-за неправильной настройки приложения, например, недействительной строки подключения к базе данных. Как результат — сбой оркестрации в рабочем процессе. С `RewindAsync` помощью API `rewind` (.NET) или (JavaScript) администратор приложения может исправить ошибку конфигурации и перемотать неудавшуюся согласованность в состояние непосредственно перед сбоем. Ни один из шагов взаимодействия человека не требуется повторно гостем, и оркестровка теперь может быть успешно выполнена.

> [!NOTE]
> Функция *перемотки* не поддерживает случаи перемотки оркестровки, в которых используются прочные таймеры.

# <a name="c"></a>[C #](#tab/csharp)

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
> Предыдущий код C-кода предназначен для функций длительного пользования 2.x. Для функций Durable 1.x `OrchestrationClient` необходимо использовать `DurableClient` атрибут вместо атрибута, а `IDurableOrchestrationClient`вместо этого — тип `DurableOrchestrationClient` параметра. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Смотрите [примеры Start](#javascript-function-json) для конфигурации function.json.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Вы также можете перемотать экземпляр оркестровки непосредственно с помощью команды `durable rewind` [«Основные инструменты функций Azure».](../functions-run-local.md) Она принимает следующие параметры.

* (требуется) : Идентификатор экземпляра оркестровки. ** `id` **
* (необязательно) : Причина перемотки экземпляра оркестровки. ** `reason` **
* (необязательно) : Название параметра приложения, содержащего строку подключения к хранилищу для использования. ** `connection-string-setting` ** Значение по умолчанию — `AzureWebJobsStorage`.
* (необязательно) : Название концентратора задач «Долговечные функции» для использования. ** `task-hub-name` ** По умолчанию используется имя концентратора задач в файле [host.json.](durable-functions-bindings.md#host-json)

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>История экземпляра очистки

Чтобы удалить все данные, связанные с оркестровкой, можно очистить историю экземпляров. Например, можно удалить любые строки таблицы Azure и большие капли сообщений, связанные с завершенным экземпляром. Для этого используйте `PurgeInstanceHistoryAsync` метод (.NET) или `purgeInstanceHistory` (JavaScript) [связывания клиента orchestration.](durable-functions-bindings.md#orchestration-client)

Этот метод имеет две перегрузки. Первая история перегрузок очищает идентификатор экземпляра оркестровки:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

Смотрите [примеры Start](#javascript-function-json) для конфигурации function.json.

---

В следующем примере показана функция, инициированная таймером, которая очищает историю для всех экземпляров оркестровки, завершенных после заданного интервала времени. В этом случае он удаляет данные для всех экземпляров, завершенных 30 или более дней назад. Он планируется запустить один раз в день, в 12:00:

# <a name="c"></a>[C #](#tab/csharp)

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
> Предыдущий код C-кода предназначен для функций длительного пользования 2.x. Для функций Durable 1.x `OrchestrationClient` необходимо использовать `DurableClient` атрибут вместо атрибута, а `IDurableOrchestrationClient`вместо этого — тип `DurableOrchestrationClient` параметра. Для получения дополнительной информации о [Durable Functions versions](durable-functions-versions.md) различиях между версиями см.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Метод `purgeInstanceHistoryBy` может быть использован для условной очистки истории экземпляров для нескольких экземпляров.

**function.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
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

> [!NOTE]
> Этот пример нацелен на версию Durable Functions 2.x. В версии 1.x `orchestrationClient` `durableClient`используйте вместо .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```

---

> [!NOTE]
> Для успешной операции по завершению операции по завершению операции «История очистки» состояние времени выполнения целевого экземпляра должно быть **завершено,** завершено или **не выполнено.** **Terminated**

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Можно очистить историю экземпляра оркестровки с помощью команды `durable purge-history` [«Основные инструменты функций Azure».](../functions-run-local.md) Подобно второму примеру C-из-за предыдущего раздела, он очищает историю для всех экземпляров оркестровки, созданных в течение определенного интервала времени. Кроме того, можно фильтровать продуваемые экземпляры по состоянию времени выполнения. Команда имеет несколько параметров:

* (необязательно) : Очистите историю экземпляров, созданных после этой даты/времени (UTC). ** `created-after` ** Принимаются значения даты и времени в формате ISO 8601.
* (необязательно) : Очистите историю экземпляров, созданных до этой даты/времени (UTC). ** `created-before` ** Принимаются значения даты и времени в формате ISO 8601.
* (необязательно) : Очистка истории экземпляров с определенным статусом (например, запущенной или завершенной). ** `runtime-status` ** Можно указать несколько состояний (через пробел).
* (необязательно) : Название параметра приложения, содержащего строку подключения к хранилищу для использования. ** `connection-string-setting` ** Значение по умолчанию — `AzureWebJobsStorage`.
* (необязательно) : Название концентратора задач «Долговечные функции» для использования. ** `task-hub-name` ** По умолчанию используется имя концентратора задач в файле [host.json.](durable-functions-bindings.md#host-json)

Следующая команда удаляет историю всех неудачных экземпляров, созданных до 14 ноября 2018 года в 19:35 (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Удаление концентратора задач

Используя команду `durable delete-task-hub` [«Основные инструменты функций Azure»](../functions-run-local.md) можно удалить все артефакты хранения, связанные с определенным концентратором задач, включая таблицы хранения Azure, очереди и капли. Команда имеет два параметра:

* (необязательно) : Название параметра приложения, содержащего строку подключения к хранилищу для использования. ** `connection-string-setting` ** Значение по умолчанию — `AzureWebJobsStorage`.
* (необязательно) : Название концентратора задач «Долговечные функции» для использования. ** `task-hub-name` ** По умолчанию используется имя концентратора задач в файле [host.json.](durable-functions-bindings.md#host-json)

Следующая команда удаляет все данные хранения `UserTest` Azure, связанные с концентратором задач.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Сведения об обработке управления версиями](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Встроенная ссылка HTTP API на управление например](durable-functions-http-api.md)
