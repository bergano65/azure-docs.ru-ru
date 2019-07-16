---
title: Создание конечной точки RESTful для настраиваемых поставщиков
description: В этом учебнике рассказывается, как создать конечную точку RESTful для пользовательских поставщиков. Здесь подробно описано, как обрабатывать запросы и ответы для поддерживаемых методов HTTP RESTful.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 176e3b02cbda7577e306d86363cfe5b41335fb6e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799183"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Создание конечной точки RESTful для настраиваемых поставщиков

Настраиваемые поставщики позволяют настраивать рабочие процессы в Azure. Настраиваемый поставщик представляет собой контракт между Azure и `endpoint`. В этом учебнике описывается процесс создания пользовательского поставщика RESTful `endpoint`. Если вы не знакомы с настраиваемыми поставщиками Azure, перейдите к этой [обзорной статье](./custom-providers-overview.md).

Это руководство разделено на следующие разделы:

- Работа с пользовательскими действиями и пользовательскими ресурсами.
- Секционирование пользовательских ресурсов в хранилище.
- Поддержка методов пользовательских поставщиков RESTful.
- Интеграция операций RESTful.

Это руководство основано на следующих руководствах:

- [Настройка Функций Azure для настраиваемых поставщиков Azure](./tutorial-custom-providers-function-setup.md)

> [!NOTE]
> Этот учебник создан на основе предыдущего учебника. Некоторые шаги в учебнике будут работать только в том случае, если Функция Azure настроена для работы с настраиваемыми поставщиками.

## <a name="working-with-custom-actions-and-custom-resources"></a>Работа с пользовательскими действиями и пользовательскими ресурсами

В этом учебнике описано, как обновить функцию, чтобы она работала в качестве конечной точки RESTful для нашего пользовательского поставщика. В Azure ресурсы и действия создаются после базовой спецификации RESTful: PUT — создает ресурс, GET (экземпляр) — извлекает существующий ресурс, DELETE — удаляет существующий ресурс, POST — инициирует действие, а GET (коллекция) — перечисляет все существующие ресурсы. В этом учебнике мы будем использовать Хранилище таблиц Azure, но подойдет любая база данных или служба хранилища.

## <a name="how-to-partition-custom-resources-in-storage"></a>Секционирование пользовательских ресурсов в хранилище

Так как мы создаем службу RESTful, нам нужно хранить созданные ресурсы в хранилище. Для Хранилища таблиц Azure нам необходимо создать ключи секций и строк для наших данных. Для пользовательских поставщиков данные должны быть секционированы. Когда входящий запрос отправляется пользовательскому поставщику, поставщик добавляет заголовок `x-ms-customproviders-requestpath` к исходящему запросу в `endpoint`.

Пример заголовка `x-ms-customproviders-requestpath` для пользовательского ресурса:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Основываясь на приведенном выше примере заголовка `x-ms-customproviders-requestpath`, мы можем создать для нашего хранилища partitionKey и rowKey следующим образом:

Параметр | Шаблон | ОПИСАНИЕ
---|---
partitionKey | {ИД подписки}:{имя группы ресурсов}:{имя поставщика ресурсов} | Параметр partitionKey — это способ секционирования данных. В большинстве случаев данные должны быть секционированы с помощью экземпляра пользовательского поставщика.
rowKey | {тип моего ресурса}:{имя моего ресурса} | RowKey — это индивидуальный идентификатор данных. В большинстве случаев это имя ресурса.

Кроме того, нам также необходимо создать новый класс для моделирования нашего пользовательского ресурса. В этом учебнике мы добавим в нашу функцию класс `CustomResource`, который является универсальным классом, принимающим любые введенные данные:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```

Будет создан базовый класс на основе `TableEntity`, который используется для хранения данных. Класс `CustomResource` наследует два свойства от `TableEntity`: partitionKey и rowKey.

## <a name="support-custom-provider-restful-methods"></a>Поддержка методов пользовательских поставщиков RESTful

> [!NOTE]
> Если вы не копируете код непосредственно из учебника, содержимое ответа должно быть допустимым объектом JSON с заголовком `Content-Type` типа `application/json`.

Теперь, когда мы выполнили настройку секционирования данных, давайте разберем основные методы CRUD и триггера для пользовательских ресурсов и пользовательских действий. Так как пользовательские поставщики действуют как прокси-серверы, запрос и ответ должны создаваться и обрабатываться с помощью RESTful `endpoint`. Выполните приведенные ниже фрагменты кода для обработки основных операций RESTful:

### <a name="trigger-custom-action"></a>Активация пользовательского действия

Для пользовательских поставщиков настраиваемое действие запускается с помощью запросов `POST`. Пользовательское действие может дополнительно принимать текст запроса, который содержит набор входных параметров. Затем действие должно вернуть ответный сигнал о результате действия, а также о том, было ли оно успешным. В этом учебнике мы добавим метод `TriggerCustomAction` к нашей функции:

```csharp
/// <summary>
/// Triggers a custom action with some side effect.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <returns>The http response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

Метод `TriggerCustomAction` принимает входящий запрос и просто возвращает ответ с кодом состояния успешного выполнения операции. 

### <a name="create-custom-resource"></a>Создание настраиваемого ресурса

Для пользовательских поставщиков настаиваемый ресурс создается с помощью запросов `PUT`. Пользовательский поставщик принимает текст запроса JSON, который содержит набор свойств для настраиваемого ресурса. В Azure ресурсы соответствуют модели RESTful. URL-адрес запроса, который использовался для создания ресурса, также должен иметь возможность извлекать и удалять ресурс. В этом учебнике мы добавим метод `CreateCustomResource` для создания ресурсов:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="azureResourceId">The parsed Azure resource Id.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

Метод `CreateCustomResource` добавляет во входящий запрос определенные поля Azure: `id`, `name` и `type`. Это свойства верхнего уровня, которые используются службами в Azure. Они позволят настраиваемому поставщику интегрироваться с другими службами, такими как Политика Azure, шаблоны Azure Resource Manager и журналы действий Azure.

Свойство | Образец | ОПИСАНИЕ
---|---|---
name | {myCustomResourceName} | Название настраиваемого ресурса.
Тип | Microsoft.CustomProviders/resourceProviders/{имя типа ресурса} | Пространство имен типа ресурса.
id | /subscriptions/{ИД подписки}/resourceGroups/{имя группы ресурсов}/<br>providers/Microsoft.CustomProviders/resourceProviders/{имя поставщика ресурсов}/<br>{имя типа ресурса}/{имя настраиваемого ресурса} | Идентификатор ресурса.

Помимо добавления свойств, мы также сохраняем документ в Хранилище таблиц Azure. 

### <a name="retrieve-custom-resource"></a>Извлечение настраиваемого ресурса

Для пользовательских поставщиков настаиваемый ресурс извлекается с помощью запросов `GET`. Пользовательский поставщик *не будет* принимать текст запроса JSON. В случае запросов `GET` **конечная точка** должна использовать заголовок `x-ms-customproviders-requestpath` для возврата уже созданного ресурса. В этом учебнике мы добавим метод `RetrieveCustomResource`, чтобы извлечь ресурсы:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

В Azure ресурсы должны соответствовать модели RESTful. URL-адрес запроса, который создал ресурс, должен также возвращать ресурс, если выполняется запрос `GET`.

### <a name="remove-custom-resource"></a>Удаление настраиваемого ресурса

Для пользовательских поставщиков настаиваемый ресурс удаляется с помощью запросов `DELETE`. Пользовательский поставщик *не будет* принимать текст запроса JSON. В случае запросов `DELETE` **конечная точка** должна использовать заголовок `x-ms-customproviders-requestpath` для удаления уже созданного ресурса. В этом учебнике мы добавим метод `RemoveCustomResource`, чтобы удалить ресурсы:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the result of the delete.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

В Azure ресурсы должны соответствовать модели RESTful. URL-адрес запроса, который создал ресурс, должен также удалять ресурс, если выполняется запрос `DELETE`.

### <a name="list-all-custom-resources"></a>Список всех настраиваемых ресурсов

Список существующих настраиваемых ресурсов для пользовательских поставщиков можно перечислить с помощью запросов `GET` коллекции. Пользовательский поставщик *не будет* принимать текст запроса JSON. В случае запросов коллекции `GET` `endpoint` должна использовать заголовок `x-ms-customproviders-requestpath` для перечисления уже созданных ресурсов. В этом учебнике мы добавим метод `EnumerateAllCustomResources`, чтобы перечислить ресурсы:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The http response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> Для выполнения запроса startswith для строк ключ строки должен быть больше или меньше синтаксиса таблицы Azure. 

Для перечисления всех существующих ресурсов создадим запрос таблицы Azure, который проверяет, что ресурсы существуют в секции настраиваемого поставщика. Затем запрос проверяет, что ключ строки начинается с того же типа `{myResourceType}`.

## <a name="integrate-restful-operations"></a>Интеграция операций RESTful

Когда все методы RESTful будут добавлены в функцию, можно обновить основной метод `Run`, чтобы вызвать функции для обработки различных запросов REST:

```csharp
/// <summary>
/// Entry point for the Azure Function webhook and acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <returns>The http response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for an custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
``` 

Обновленный метод `Run` теперь будет содержать входную привязку `tableStorage`, добавленную для Хранилища таблиц Azure. Первая часть метода теперь будет читать заголовок `x-ms-customproviders-requestpath` и использовать библиотеку `Microsoft.Azure.Management.ResourceManager.Fluent` для анализа значения в качестве идентификатора ресурса. Заголовок `x-ms-customproviders-requestpath` отправляется пользовательским поставщиком. Он обозначает путь входящего запроса. Используя проанализированный идентификатор ресурса, теперь можно создать partitionKey и rowKey для данных, чтобы выполнять поиск или хранить настраиваемые ресурсы.

Помимо добавления методов и классов нам необходимо обновить используемые методы для функции. Добавьте следующий код в начало файла.

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent.dll"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

В случае необходимости пример готового кода можно найти в [справочнике по конечной точке C# RESTful ​​пользовательского поставщика](./reference-custom-providers-csharp-endpoint.md). После создания функции сохраните URL-адрес, по которому можно запускать функцию, так как она будет использоваться в последующих учебниках.

## <a name="next-steps"></a>Дополнительная информация

Выполнив действия в этой статье, вы создали конечную точку RESTful для работы с `endpoint` настраиваемого поставщика Azure. Чтобы узнать, как создать собственный поставщик, перейдите к следующей статье.

- [Руководство. Создание пользовательского поставщика](./tutorial-custom-providers-create.md)
