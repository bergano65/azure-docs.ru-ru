---
title: Триггер Azure Функции HTTP
description: Узнайте, как вызвать функцию Azure через HTTP.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 045f3ccdc8dc09bf657ab39ce15a0d0524c73fcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277600"
---
# <a name="azure-functions-http-trigger"></a>Триггер Azure Функции HTTP

Триггер HTTP позволяет вызвать функцию с помощью HTTP-запроса. Триггер HTTP можно использовать для создания независимых от сервера API-интерфейсов и для ответа веб-перехватчикам.

Значение возврата по умолчанию для функции HTTP-триггера:

- `HTTP 204 No Content`с пустым телом в функциях 2.x и выше
- `HTTP 200 OK`с пустым телом в Функциях 1.x

Чтобы изменить ответ HTTP, назначаем [выходную привязку.](./functions-bindings-http-webhook-output.md)

Для получения дополнительной информации о привязки HTTP, см [обзор](./functions-bindings-http-webhook.md) и [выход связывающей ссылки](./functions-bindings-http-webhook-output.md).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Пример

# <a name="c"></a>[C #](#tab/csharp)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), выполняющая поиск параметра `name` в строке запроса или в тексте HTTP-запроса. Обратите внимание, что возвращаемое значение используется для привязки выходных данных, но атрибут возвращаемого значения не является обязательным.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

В следующем примере показаны привязка триггера в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. В функции выполняется поиск параметра `name` в строке запроса или в тексте HTTP-запроса.

Ниже показан файл *function.json*.

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

В разделе [Конфигурация](#configuration) описываются эти свойства.

Ниже приведен код сценария C#, который выполняет привязку к `HttpRequest`:

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

Вы можете выполнить привязку к пользовательскому объекту вместо `HttpRequest`. Этот объект создается из текста запроса и анализируется как JSON. Аналогичным образом, тип может быть передан привязке к выходу `200` http и возвращен в качестве органа ответа, а также кодстатусов.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

В следующем примере показана привязка триггера в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует привязку. В функции выполняется поиск параметра `name` в строке запроса или в тексте HTTP-запроса.

Ниже показан файл *function.json*.

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

В разделе [Конфигурация](#configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

В следующем примере показаны привязка триггера в файле *function.json* и [функция Python](functions-reference-python.md), использующая эту привязку. В функции выполняется поиск параметра `name` в строке запроса или в тексте HTTP-запроса.

Ниже показан файл *function.json*.

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

В разделе [Конфигурация](#configuration) описываются эти свойства.

Ниже приведен код Python.

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

# <a name="java"></a>[Java](#tab/java)

* [Чтение параметра из строки запроса](#read-parameter-from-the-query-string)
* [Чтение текста из запроса POST](#read-body-from-a-post-request)
* [Чтение параметра из маршрута](#read-parameter-from-a-route)
* [Чтение текста POJO из запроса POST](#read-pojo-body-from-a-post-request)

Следующие примеры показывают связывание триггера HTTP.

#### <a name="read-parameter-from-the-query-string"></a>Чтение параметра из строки запроса

Этот пример считывает параметр с именем `id` из строки запроса и использует его, чтобы создать документ JSON, возвращаемый клиенту, с типом содержимого `application/json`.

```java
@FunctionName("TriggerStringGet")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<String>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("GET parameters are: " + request.getQueryParameters());

    // Get named parameter
    String id = request.getQueryParameters().getOrDefault("id", "");

    // Convert and display
    if (id.isEmpty()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String name = "fake_name";
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-body-from-a-post-request"></a>Чтение текста из запроса POST

Этот пример считывает текст из запроса POST как `String` и использует его, чтобы создать документ JSON, возвращаемый клиенту, с типом содержимого `application/json`.

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route"></a>Чтение параметра из маршрута

Этот пример считывает обязательный параметр с именем `id` и необязательный параметр `name` из пути маршрута и использует их для создания документа JSON, возвращаемого клиенту, с типом содержимого `application/json`. T

```java
@FunctionName("TriggerStringRoute")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS,
            route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
        HttpRequestMessage<Optional<String>> request,
        @BindingName("id") String id,
        @BindingName("name") String name,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Route parameters are: " + id);

    // Convert and display
    if (id == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-pojo-body-from-a-post-request"></a>Чтение текста POJO из запроса POST

Ниже приведен код для класса `ToDoItem`, указанного в этом примере:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

Этот пример считывает текст запроса POST. Текст запроса автоматически десериализуется в объект `ToDoItem` и возвращается к клиенту с типом содержимого `application/json`. Параметр `ToDoItem` сериализуется средой выполнения службы "Функции", так как он назначен свойству `body` класса `HttpMessageResponse.Builder`.

```java
@FunctionName("TriggerPojoPost")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.POST}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<ToDoItem>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Request body is: " + request.getBody().orElse(null));

    // Check request body
    if (!request.getBody().isPresent()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final ToDoItem body = request.getBody().get();
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(body)
                        .build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Атрибуты и аннотации

В [библиотеках классов C](functions-dotnet-class-library.md) `HttpTrigger` и Java атрибут доступен для настройки функции.

Можно установить уровень авторизации и допустимые методы HTTP в параметрах конструктора атрибутов, типе webhook и шаблоне маршрута. Для получения дополнительной информации об этих настройках [см.](#configuration)

# <a name="c"></a>[C #](#tab/csharp)

В этом примере показано, как использовать атрибут [HttpTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs)

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Для полного примера см. [пример триггера.](#example)

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Атрибуты не поддерживаются скриптом C'.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Атрибуты не поддерживаются JavaScript.

# <a name="python"></a>[Python](#tab/python)

Атрибуты не поддерживаются Python.

# <a name="java"></a>[Java](#tab/java)

В этом примере показано, как использовать атрибут [HttpTrigger.](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java)

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Для полного примера см. [пример триггера.](#example)

---

## <a name="configuration"></a>Параметр Configuration

В следующей таблице объясняется свойства связывающей конфигурации, `HttpTrigger` установленные в файле *function.json* и атрибуте.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
| **тип** | Недоступно| Обязательное. Необходимо задать значение `httpTrigger`. |
| **direction** | Недоступно| Обязательное. Необходимо задать значение `in`. |
| **name** | Недоступно| Обязательное. Имя переменной, из которой в коде функции можно получить запрос или текст запроса. |
| <a name="http-auth"></a>**authLevel** |  **АутЛевл** |Определяет, какие ключи (если они требуются) должны присутствовать в запросе, чтобы вызвать функцию. Уровень авторизации может принимать одно из следующих значений: <ul><li><code>anonymous</code>&mdash; — ключи API не требуются.</li><li><code>function</code>&mdash; — требуется ключ API для конкретной функции. Это значение используется по умолчанию, если не указано иное.</li><li><code>admin</code>&mdash; — требуется главный ключ.</li></ul> Дополнительные сведения см. в разделе [Ключи авторизации](#authorization-keys). |
| **Методы** |**Методы** | Массив методов HTTP, на которые отвечает функция. Если свойство не указано, функция отвечает на все методы HTTP. Просмотрите [настройку конечной точки HTTP.](#customize-the-http-endpoint) |
| **Маршрут** | **Маршрут** | Шаблон маршрута, определяющий URL-адреса запросов, на которые отвечает функция. Если значение не указано, по умолчанию используется `<functionname>`. Для получения дополнительной [customize the HTTP endpoint](#customize-the-http-endpoint)информации см. |
| **webHookType** | **WebHookType** | _Поддерживается только для среды выполнения версии 1.x._<br/><br/>Настраивает триггер HTTP, чтобы выступать в качестве приемника [веб-крючка](https://en.wikipedia.org/wiki/Webhook) для указанного поставщика. Если вы установите это свойство, не устанавливайте свойство `methods`. Тип веб-перехватчика может принимать одно из следующих значений:<ul><li><code>genericJson</code>&mdash; — конечная точка веб-перехватчика общего назначения без логики для конкретного поставщика. Этот параметр определяет, что принимаются только запросы HTTP POST с содержимым типа `application/json`.</li><li><code>github</code>&mdash;Функция реагирует на [веб-крючки GitHub.](https://developer.github.com/webhooks/) Не используйте свойство _authLevel_ вместе с веб-перехватчиками GitHub. Дополнительные сведения см. в этой статье, в разделе веб-перехватчики GitHub.</li><li><code>slack</code>&mdash;Функция реагирует на [Slack webhooks.](https://api.slack.com/outgoing-webhooks) Не используйте свойство _authLevel_ вместе с веб-перехватчиками Slack. Дополнительные сведения см. в разделе о веб-перехватчиках Slack далее в этой статье.</li></ul>|

## <a name="payload"></a>Полезные данные

Тип ввода триггера объявляется как тип пользовательского `HttpRequest` или пользовательского. Если вы выберете `HttpRequest`, то получите полный доступ к объекту запроса. При объявлении пользовательского типа среда выполнения попытается проанализировать текст запроса JSON для задания свойств объекта.

## <a name="customize-the-http-endpoint"></a>Настройка конечной точки HTTP

По умолчанию при создании функции для триггера HTTP маршрут для ее адресации имеет следующий вид:

    http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>

Этот маршрут можно настроить с помощью дополнительного свойства `route` привязки для вывода триггера HTTP. Например, приведенный ниже файл *function.json* определяет свойство `route` для триггера HTTP.

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

При такой конфигурации функция доступна по приведенному ниже маршруту вместо исходного.

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Эта конфигурация позволяет функционному коду поддерживать два параметра в адресе, _категории_ и _идентификаторе._

# <a name="c"></a>[C #](#tab/csharp)

Для параметров можно использовать любое [ограничение маршрута веб-API](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints). Приведенный ниже код функции C# используют оба параметра.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Для параметров можно использовать любое [ограничение маршрута веб-API](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints). Приведенный ниже код функции C# используют оба параметра.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

В узелвремени время выполнения функций обеспечивает `context` тело запроса от объекта. Дополнительные сведения см. в разделе [Пример JavaScript в триггере](#example).

Ниже приводится следующий пример, `context.bindingData`как читать параметры маршрута из .

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="python"></a>[Python](#tab/python)

Контекст выполнения функции разоблачается через параметр, объявленный как `func.HttpRequest`. Этот экземпляр позволяет функции получить доступ к параметрам маршрута данных, значениям строки запроса и методам, которые позволяют возвращать ответы HTTP.

После определения параметры маршрута доступны функции, вызывая `route_params` метод.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="java"></a>[Java](#tab/java)

Контекст выполнения функции — это `HttpTrigger` свойства, объявленные в атрибуте. Атрибут позволяет определить параметры маршрута, уровни авторизации, глаголы HTTP и экземпляр входящего запроса.

Параметры маршрута определяются `HttpTrigger` через атрибут.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

---

По умолчанию все маршруты функций начинаются с префикса *api*. Вы можете настроить или удалить этот префикс с помощью свойства `http.routePrefix` в файле [host.json](functions-host-json.md). В следующем примере префикс маршрута *api* удаляется. Он заменяется пустой строкой в файле *host.json*.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

## <a name="using-route-parameters"></a>Использование параметров маршрута

Параметры маршрута, определяющие `route` шаблон функции, доступны для каждого привязки. Например, если у вас `"route": "products/{id}"` есть маршрут, определенный как привязка к таблице хранения, можно использовать значение `{id}` параметра в связывающей конфигурации.

Следующая конфигурация `{id}` показывает, как параметр `rowKey`передается в привязку.

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

## <a name="working-with-client-identities"></a>Работа с удостоверениями клиентов

Если в приложении-функции используется [аутентификация и авторизация Службы приложений](../app-service/overview-authentication-authorization.md), сведения об аутентифицированных клиентах можно посмотреть прямо в коде. Эта информация доступна в виде [заголовков запросов, которые вставляет платформа](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Эти сведения также можно считывать из данных привязки. Эта возможность доступна только для выполнения функций в 2.x и выше. Кроме того, сейчас она доступна только для языков .NET.

# <a name="c"></a>[C #](#tab/csharp)

Информация о клиентах, удостоверяемых подлинностью, доступна в качестве [claimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal доступен как часть контекста запроса, как показано в следующем примере:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Кроме того, ClaimsPrincipal можно включить как дополнительный параметр в сигнатуру функции:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Информация о клиентах, удостоверяемых подлинностью, доступна в качестве [claimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal доступен как часть контекста запроса, как показано в следующем примере:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Кроме того, ClaimsPrincipal можно включить как дополнительный параметр в сигнатуру функции:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Аутентифицированный пользователь доступен через [заголовки HTTP.](../app-service/app-service-authentication-how-to.md#access-user-claims)

# <a name="python"></a>[Python](#tab/python)

Аутентифицированный пользователь доступен через [заголовки HTTP.](../app-service/app-service-authentication-how-to.md#access-user-claims)

# <a name="java"></a>[Java](#tab/java)

Аутентифицированный пользователь доступен через [заголовки HTTP.](../app-service/app-service-authentication-how-to.md#access-user-claims)

---

## <a name="authorization-keys"></a>Ключи авторизации

Служба "Функции" позволяет использовать ключи, чтобы затруднить несанкционированный доступ к конечным точкам функции HTTP во время развертывания.  Если уровень авторизации HTTP на функции `anonymous`срабатывания HTTP не установлен, запросы должны включать ключ API в запросе. 

> [!IMPORTANT]
> Хотя ключи могут помочь в маскировке конечных точек HTTP во время развертывания, они не предназначены для защиты триггера HTTP в рабочей среде. Дополнительные сведения см. в разделе [Защита конечной точки HTTP в рабочей среде](#secure-an-http-endpoint-in-production).

> [!NOTE]
> В среде выполнения службы "Функции" версии 1.х веб-перехватчик может использовать ключи для авторизации запросов различными способами, в зависимости от поддерживаемых поставщиком технологий. Это рассматривается в разделе [Веб-перехватчики и ключи](#webhooks-and-keys). Время выполнения функций в версии 2.x и выше не включает встроенную поддержку поставщиков веб-крючков.

#### <a name="authorization-scopes-function-level"></a>Области авторизации (уровень функции)

Существует два области авторизации для ключей функционального уровня:

* **Функция**: Эти клавиши применяются только к определенным функциям, под которыми они определены. Если такой ключ используется в качестве ключа API, он предоставляет доступ только к определенной функции.

* **Ключи host**: Ключи с областью хоста могут быть использованы для доступа ко всем функциям в приложении функции. Если такой ключ используется в качестве ключа API, он предоставляет доступ к любой функции в приложении-функции. 

Каждый ключ имеет имя для удобства использования, а один из ключей (с именем default) используется как ключ по умолчанию на уровне узла и функции. Ключи функций имеют приоритет над ключами узла. Если определены два ключа с одним именем, всегда используется ключ функции.

#### <a name="master-key-admin-level"></a>Мастер-ключ (уровень админ) 

Каждое приложение функции также имеет ключ хоста уровня админ с именем. `_master` Помимо предоставления доступа на уровне хоста ко всем функциям приложения, главный ключ также обеспечивает административный доступ к aA, наиболее многофункциональным aIS времени выполнения. Его невозможно отозвать. При задании уровня авторизации `admin` запросы должны использовать главный ключ. Использование любого другого ключа приведет к ошибке авторизации.

> [!CAUTION]  
> Главный ключ предоставляет высокий уровень разрешений в приложении-функции, поэтому никогда не передавайте этот ключ третьим лицам и не включайте его в состав клиентских приложений. Соблюдайте осторожность при выборе уровня авторизации для администратора.

## <a name="obtaining-keys"></a>Получение ключей

Ключи хранятся в Azure в составе приложения-функции в зашифрованном виде. Чтобы просмотреть ключи, создать новые или сменить значения ключей, откройте на [портале Azure](https://portal.azure.com) нужную функцию, активируемую по HTTP, и выберите **Управление**.

![Управляйте ключами функций на портале.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Вы можете получить функциональные ключи программно, используя [AI управления ключами.](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)

## <a name="api-key-authorization"></a>Проверка подлинности с помощью ключа API

Большинству шаблонов триггеров HTTP требуется ключ API в запросе. Поэтому HTTP-запрос обычно выглядит как следующий URL-адрес:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

Ключ можно передать в переменной строки запроса с именем `code`, как показано выше. Его также можно включить в заголовок HTTP `x-functions-key`. Значением ключа может быть любой ключ функции, определенный для запрашиваемой функции, или любой ключ узла.

Вы можете разрешить анонимные запросы, не требующие наличие ключей. Вы также можете потребовать использования основного ключа. Уровень авторизации по умолчанию можно изменить с помощью свойства `authLevel` в объекте JSON для привязки. Дополнительные сведения см. в разделе [Конфигурация триггера](#configuration).

> [!NOTE]
> При запуске функций локально авторизация отключается независимо от указанного уровня авторизации. После публикации в Azure в триггере применяется параметр `authLevel`. Ключи по-прежнему необходимы при [локальном запуске в контейнере.](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally)


## <a name="secure-an-http-endpoint-in-production"></a>Защита конечной точки HTTP в рабочей среде

Чтобы полностью защитить функции конечных точек в рабочей среде, необходимо реализовать один из следующих вариантов безопасности на уровне приложения-функции:

* Включить аутентификацию и авторизацию в Службе приложений для приложения-функции. Платформа App Service позволяет использовать Активный каталог Azure (AAD) и несколько сторонних поставщиков идентификационных данных для проверки подлинности клиентов. Эту стратегию можно использовать для реализации пользовательских правил авторизации для своих функций, а также работать с информацией о пользователе из кода функции. Дополнительные сведения см. в разделе [Работа с удостоверениями клиентов](#working-with-client-identities) и статье [Проверка подлинности и авторизация в службе приложений Azure](../app-service/overview-authentication-authorization.md).

* Используйте службу управления API Azure (APIM) для аутентификации запросов. APIM предоставляет широкий набор параметров безопасности API для входящих запросов. Дополнительные сведения см. [в статье о политиках аутентификации службы "Управление API"](../api-management/api-management-authentication-policies.md). С помощью службы "Управление API" можно настроить приложение-функцию на прием запросов только с IP-адреса вашего экземпляра этой службы. Дополнительные сведения см. в разделе [Ограничения IP-адресов](ip-addresses.md#ip-address-restrictions).

* Разверните приложение-функцию в среде службы приложений Azure (ASE). ASE предоставляет выделенную среду размещения, в которой можно выполнять функции. ASE позволяет настроить один интерфейсный шлюз, который можно использовать для аутентификации всех входящих запросов. Дополнительные сведения см. в статье [Настройка брандмауэра веб-приложения (WAF) для среды службы приложений](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

При использовании одного из этих методов безопасности уровня приложения, вы должны `anonymous`установить уровень авторизации функции HTTP-срабатывается.

## <a name="webhooks"></a>Веб-перехватчики

> [!NOTE]
> Режим веб-перехватчика доступен только для версии 1.x среды выполнения функций. Это изменение было сделано для повышения производительности триггеров HTTP в версии 2.x и выше.

В версии 1.x шаблоны веб-перехватчика обеспечивают дополнительную проверку полезных данных веб-перехватчика. В версии 2.x и выше, базовый триггер HTTP по-прежнему работает и является рекомендуемым подходом для webhooks. 

### <a name="github-webhooks"></a>Веб-перехватчики GitHub

Чтобы настроить ответ на вызовы веб-перехватчика GitHub, прежде всего создайте функцию с триггером HTTP, для которого свойство **webHookType** будет иметь значение `github`. Затем скопируйте его URL-адрес и ключ API в репозиторий GitHub, используя страницу **Добавить веб-перехватчик**. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Веб-перехватчики Slack

Webhook Slack создает маркер автоматически и не позволяет вам задать его, поэтому необходимо настроить ключ для конкретной функции с использованием маркера, предоставленного Slack. Ознакомьтесь с разделом [Ключи авторизации](#authorization-keys).

## <a name="webhooks-and-keys"></a>Веб-перехватчики и ключи

Авторизация веб-перехватчика обрабатывается компонентом получателя веб-перехватчика, который входит в состав триггера HTTP. Механизм обработки различается в зависимости от типа веб-перехватчика. Каждый из этих механизмов зависит от ключей. По умолчанию используется ключ функции с именем default. Чтобы использовать другой ключ, необходимо настроить поставщик веб-перехватчика так, чтобы он отправлял имя ключа в составе запроса одним из следующих способов.

* **В строке запроса**: поставщик передает имя ключа в параметре `clientid` строки запроса, например `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **В заголовке запроса**: поставщик передает имя ключа в заголовке `x-functions-clientid`.

## <a name="limits"></a>Ограничения

Длина HTTP-запроса ограничена 100 МБ (104 857 600 байт), а длина URL-адреса — 4 КБ (4096 байт). Эти ограничения задает элемент `httpRuntime`[файла Web.config](https://github.com/Azure/azure-functions-host/blob/3.x/src/WebJobs.Script.WebHost/web.config) среды выполнения.

Если функция, используюая триггер HTTP, не выполняется в течение 230 секунд, [балансер загрузки Azure](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) тайм-аут и возвращает ошибку HTTP 502. Функция продолжит работу, но вернуть ответ HTTP будет невозможно. Для долго выполняющихся функций рекомендуем следовать асинхронным шаблонам и возвращать расположение, в котором можно проверить состояние запроса. Чтобы узнать, как долго может выполняться функция, см. раздел [Масштабирование и размещение — план](functions-scale.md#timeout).


## <a name="next-steps"></a>Дальнейшие действия

- [Возврат ответа HTTP от функции](./functions-bindings-http-webhook-output.md)
