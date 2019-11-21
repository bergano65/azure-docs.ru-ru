---
title: Триггеры и привязки HTTP в службе "Функции Azure"
description: Узнайте, как использовать триггеры и привязки HTTP в службе "Функции Azure".
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 598074a6d5093c4febd4d62266a1c852200e3f69
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231171"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Триггеры и привязки HTTP в службе "Функции Azure"

В этой статье объясняется, как использовать триггеры и выходные привязки в службе "Функции Azure".

Триггер HTTP можно настроить на ответ [веб-перехватчикам](https://en.wikipedia.org/wiki/Webhook).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

Для кода в этой статье по умолчанию используется синтаксис Функций 2.x, который применяется в .NET Core. Сведения о синтаксисе версии 1.x см. на странице с [соответствующими шаблонами функций](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="packages---functions-1x"></a>Пакеты – Функции 1.x

Привязки служебной шины доступны в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http), версия 1.х. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Пакеты — Функции 2.x

Привязки служебной шины доступны в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http), версия 3.х. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/).

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Триггер

Триггер HTTP позволяет вызвать функцию с помощью HTTP-запроса. Триггер HTTP можно использовать для создания независимых от сервера API-интерфейсов и для ответа веб-перехватчикам.

По умолчанию в Функциях версии 1.x триггер HTTP возвращает ответ HTTP "200 — OK" с пустым текстом, а в Функциях версии 2.x — ответ "204 — содержимое отсутствует" с пустым текстом. Чтобы изменить ответ, настройте [выходную привязку HTTP](#output).

## <a name="trigger---example"></a>Пример триггера

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C# Script](#tab/csharp-script)

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

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

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

Вы можете выполнить привязку к пользовательскому объекту вместо `HttpRequest`. Этот объект создается из текста запроса и анализируется как JSON. Аналогичным образом тип можно передать в привязку вывода ответа HTTP. Результатом будет текст ответа с кодом состояния 200.

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

В следующем примере показаны привязка триггера в файле *function.json* и [функция Python](functions-reference-python.md), которая использует эту привязку. В функции выполняется поиск параметра `name` в строке запроса или в тексте HTTP-запроса.

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

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

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

# <a name="javatabjava"></a>[Java](#tab/java)

* [Чтение параметра из строки запроса](#read-parameter-from-the-query-string)
* [Чтение текста из запроса POST](#read-body-from-a-post-request)
* [Чтение параметра из маршрута](#read-parameter-from-a-route)
* [Чтение текста POJO из запроса POST](#read-pojo-body-from-a-post-request)

В следующем примере показана привязка триггера HTTP в файле *function.json* и соответствующие [функции JavaScript](functions-reference-java.md), которые используют привязку. 

Ниже показан файл *function.json*.

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "anonymous",
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

#### <a name="read-parameter-from-the-query-string"></a>Read parameter from the query string

Этот пример считывает параметр с именем ```id``` из строки запроса и использует его, чтобы создать документ JSON, возвращаемый клиенту, с типом содержимого ```application/json```. 

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

#### <a name="read-body-from-a-post-request"></a>Read body from a POST request

Этот пример считывает текст из запроса POST как ```String``` и использует его, чтобы создать документ JSON, возвращаемый клиенту, с типом содержимого ```application/json```.

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

#### <a name="read-parameter-from-a-route"></a>Read parameter from a route

Этот пример считывает обязательный параметр с именем ```id``` и необязательный параметр ```name``` из пути маршрута и использует их для создания документа JSON, возвращаемого клиенту, с типом содержимого ```application/json```. T

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

#### <a name="read-pojo-body-from-a-post-request"></a>Read POJO body from a POST request

Ниже приведен код для класса ```ToDoItem```, указанного в этом примере:

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

Этот пример считывает текст запроса POST. Текст запроса автоматически десериализуется в объект ```ToDoItem``` и возвращается к клиенту с типом содержимого ```application/json```. Параметр ```ToDoItem``` сериализуется средой выполнения службы "Функции", так как он назначен свойству ```body``` класса ```HttpMessageResponse.Builder```.

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

## <a name="trigger---attributes"></a>Атрибуты триггера

In [C# class libraries](functions-dotnet-class-library.md) and Java, the `HttpTrigger` attribute is available to configure the function.

You can set the authorization level and allowable HTTP methods in attribute constructor parameters, webhook type, and a route template. Дополнительные сведения об этих параметрах см. в разделе [Конфигурация триггера](#trigger---configuration).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

This example demonstrates how to use the [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) attribute.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

For a complete example, see the [trigger example](#trigger---example).

# <a name="c-scripttabcsharp-script"></a>[C# Script](#tab/csharp-script)

Attributes are not supported by C# Script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attributes are not supported by JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Attributes are not supported by Python.

# <a name="javatabjava"></a>[Java](#tab/java)

This example demonstrates how to use the [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) attribute.

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

For a complete example, see the [trigger example](#trigger---example).

---

## <a name="trigger---configuration"></a>Конфигурация триггера

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `HttpTrigger`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
| **type** | Н/Д| Обязательное. Необходимо задать значение `httpTrigger`. |
| **direction** | Н/Д| Обязательное. Необходимо задать значение `in`. |
| **name** | Н/Д| Обязательное. Имя переменной, из которой в коде функции можно получить запрос или текст запроса. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Определяет, какие ключи (если они требуются) должны присутствовать в запросе, чтобы вызвать функцию. Уровень авторизации может принимать одно из следующих значений: <ul><li><code>anonymous</code>&mdash; — ключи API не требуются.</li><li><code>function</code>&mdash; — требуется ключ API для конкретной функции. Это значение используется по умолчанию, если не указано иное.</li><li><code>admin</code>&mdash; — требуется главный ключ.</li></ul> Дополнительные сведения см. в разделе [Ключи авторизации](#authorization-keys). |
| **methods** |**Методы** | Массив методов HTTP, на которые отвечает функция. Если свойство не указано, функция отвечает на все методы HTTP. Ознакомьтесь с разделом о [настройке конечной точки HTTP](#customize-the-http-endpoint). |
| **route** | **Route** | Шаблон маршрута, определяющий URL-адреса запросов, на которые отвечает функция. Если значение не указано, по умолчанию используется `<functionname>`. Дополнительные сведения см. в разделе о [настройке конечной точки HTTP](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Поддерживается только для среды выполнения версии 1.x._<br/><br/>Указывает, что триггер HTTP должен выступать в качестве получателя [веб-перехватчика](https://en.wikipedia.org/wiki/Webhook) для указанного поставщика. Если вы установите это свойство, не устанавливайте свойство `methods`. Тип веб-перехватчика может принимать одно из следующих значений:<ul><li><code>genericJson</code>&mdash; — конечная точка веб-перехватчика общего назначения без логики для конкретного поставщика. Этот параметр определяет, что принимаются только запросы HTTP POST с содержимым типа `application/json`.</li><li><code>github</code>&mdash; — функция отвечает на вызовы [веб-перехватчиков GitHub](https://developer.github.com/webhooks/). Не используйте свойство _authLevel_ вместе с веб-перехватчиками GitHub. Дополнительные сведения см. в этой статье, в разделе веб-перехватчики GitHub.</li><li><code>slack</code>&mdash; — функция отвечает на вызовы [веб-перехватчиков Slack](https://api.slack.com/outgoing-webhooks). Не используйте свойство _authLevel_ вместе с веб-перехватчиками Slack. Дополнительные сведения см. в разделе о веб-перехватчиках Slack далее в этой статье.</li></ul>|

## <a name="trigger---usage"></a>Использование триггера

The trigger input type is declared as either `HttpRequest` or a custom type. Если вы выберете `HttpRequest`, то получите полный доступ к объекту запроса. При объявлении пользовательского типа среда выполнения попытается проанализировать текст запроса JSON для задания свойств объекта.

### <a name="customize-the-http-endpoint"></a>Настройка конечной точки HTTP

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

Это позволяет использовать в коде функции два параметра, передаваемых в адресе, — _category_ и _id_.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C# Script](#tab/csharp-script)

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

In Node, the Functions runtime provides the request body from the `context` object. Дополнительные сведения см. в разделе [Пример JavaScript в триггере](#trigger---example).

The following example shows how to read route parameters from `context.bindingData`.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

The function execution context is exposed via a parameter declared as `func.HttpRequest`. This instance allows a function to access data route parameters, query string values and methods that allow you to return HTTP responses.

Once defined, the route parameters are available to the function by calling the `route_params` method.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="javatabjava"></a>[Java](#tab/java)

The function execution context is properties as declared in the `HttpTrigger` attribute. The attribute allows you to define route parameters, authorization levels, HTTP verbs and the incoming request instance.

Route parameters are defined via the `HttpTrigger` attribute.

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

### <a name="working-with-client-identities"></a>Работа с удостоверениями клиентов

Если в приложении-функции используется [аутентификация и авторизация Службы приложений](../app-service/overview-authentication-authorization.md), сведения об аутентифицированных клиентах можно посмотреть прямо в коде. Эта информация доступна в виде [заголовков запросов, которые вставляет платформа](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Эти сведения также можно считывать из данных привязки. Эта возможность доступна только в среде выполнения Функций версии 2.x. Кроме того, сейчас она доступна только для языков .NET.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Information regarding authenticated clients is available as a [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal доступен как часть контекста запроса, как показано в следующем примере:

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

# <a name="c-scripttabcsharp-script"></a>[C# Script](#tab/csharp-script)

Information regarding authenticated clients is available as a [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal доступен как часть контекста запроса, как показано в следующем примере:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

The authenticated user is available via [HTTP Headers](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="pythontabpython"></a>[Python](#tab/python)

The authenticated user is available via [HTTP Headers](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="javatabjava"></a>[Java](#tab/java)

The authenticated user is available via [HTTP Headers](../app-service/app-service-authentication-how-to.md#access-user-claims).

---

### <a name="authorization-keys"></a>Ключи авторизации

Служба "Функции" позволяет использовать ключи, чтобы затруднить несанкционированный доступ к конечным точкам функции HTTP во время развертывания.  Стандартному триггеру HTTP может потребоваться, чтобы такой ключ API был в запросе. 

> [!IMPORTANT]
> Хотя ключи могут помочь в маскировке конечных точек HTTP во время развертывания, они не предназначены для защиты триггера HTTP в рабочей среде. Дополнительные сведения см. в разделе [Защита конечной точки HTTP в рабочей среде](#secure-an-http-endpoint-in-production).

> [!NOTE]
> В среде выполнения службы "Функции" версии 1.х веб-перехватчик может использовать ключи для авторизации запросов различными способами, в зависимости от поддерживаемых поставщиком технологий. Это рассматривается в разделе [Веб-перехватчики и ключи](#webhooks-and-keys). Среда выполнения версии 2.x не включает встроенную поддержку поставщиков веб-перехватчика.

Существует два типа ключей.

* **Ключи узла**, которые являются общими для всех функций в приложении-функции. Если такой ключ используется в качестве ключа API, он предоставляет доступ к любой функции в приложении-функции.
* **Ключи функции**, которые применяются только для конкретных функций, в которых они определены. Если такой ключ используется в качестве ключа API, он предоставляет доступ только к определенной функции.

Каждый ключ имеет имя для удобства использования, а один из ключей (с именем default) используется как ключ по умолчанию на уровне узла и функции. Ключи функций имеют приоритет над ключами узла. Если определены два ключа с одним именем, всегда используется ключ функции.

Каждое приложение-функция также имеет специальный **главный ключ**. Это ключ узла с именем `_master`, который предоставляет административный доступ к API среды выполнения. Его невозможно отозвать. При задании уровня авторизации `admin` запросы должны использовать главный ключ. Использование любого другого ключа приведет к ошибке авторизации.

> [!CAUTION]  
> Главный ключ предоставляет высокий уровень разрешений в приложении-функции, поэтому никогда не передавайте этот ключ третьим лицам и не включайте его в состав клиентских приложений. Соблюдайте осторожность при выборе уровня авторизации для администратора.

### <a name="obtaining-keys"></a>Получение ключей

Ключи хранятся в Azure в составе приложения-функции в зашифрованном виде. Чтобы просмотреть ключи, создать новые или сменить значения ключей, откройте на [портале Azure](https://portal.azure.com) нужную функцию, активируемую по HTTP, и выберите **Управление**.

![Управляйте ключами функций на портале.](./media/functions-bindings-http-webhook/manage-function-keys.png)

You may obtain function keys programmatically by using [Key management APIs](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

### <a name="api-key-authorization"></a>Проверка подлинности с помощью ключа API

Большинству шаблонов триггеров HTTP требуется ключ API в запросе. Поэтому HTTP-запрос обычно выглядит как следующий URL-адрес:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

Ключ можно передать в переменной строки запроса с именем `code`, как показано выше. Его также можно включить в заголовок HTTP `x-functions-key`. Значением ключа может быть любой ключ функции, определенный для запрашиваемой функции, или любой ключ узла.

Вы можете разрешить анонимные запросы, не требующие наличие ключей. Вы также можете указать, чтобы использовался главный ключ. Уровень авторизации по умолчанию можно изменить с помощью свойства `authLevel` в объекте JSON для привязки. Дополнительные сведения см. в разделе [Конфигурация триггера](#trigger---configuration).

> [!NOTE]
> При выполнении функций локально авторизация отключается независимо от указанного параметра уровня аутентификации. После публикации в Azure в триггере применяется параметр `authLevel`. Keys are still required when running [locally in a container](functions-create-function-linux-custom-image.md#run-the-image-locally).


### <a name="secure-an-http-endpoint-in-production"></a>Защита конечной точки HTTP в рабочей среде

Чтобы полностью защитить функции конечных точек в рабочей среде, необходимо реализовать один из следующих вариантов безопасности на уровне приложения-функции:

* Включить аутентификацию и авторизацию в Службе приложений для приложения-функции. Платформа службы приложений позволяет использовать Azure Active Directory (AAD) и несколько сторонних поставщиков удостоверений для аутентификации клиентов. Эта возможность позволяет реализовать пользовательские правила авторизации для функций и работать с информацией пользователя из кода функции. Дополнительные сведения см. в разделе [Работа с удостоверениями клиентов](#working-with-client-identities) и статье [Проверка подлинности и авторизация в службе приложений Azure](../app-service/overview-authentication-authorization.md).

* Используйте службу управления API Azure (APIM) для аутентификации запросов. APIM предоставляет широкий набор параметров безопасности API для входящих запросов. Дополнительные сведения см. [в статье о политиках аутентификации службы "Управление API"](../api-management/api-management-authentication-policies.md). С помощью службы "Управление API" можно настроить приложение-функцию на прием запросов только с IP-адреса вашего экземпляра этой службы. Дополнительные сведения см. в разделе [Ограничения IP-адресов](ip-addresses.md#ip-address-restrictions).

* Разверните приложение-функцию в среде службы приложений Azure (ASE). ASE предоставляет выделенную среду размещения, в которой можно выполнять функции. ASE позволяет настроить один интерфейсный шлюз, который можно использовать для аутентификации всех входящих запросов. Дополнительные сведения см. в статье [Настройка брандмауэра веб-приложения (WAF) для среды службы приложений](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

При использовании одного из этих методов обеспечения безопасности на уровне приложения-функции необходимо задать для функции, активируемой по HTTP, уровень аутентификации `anonymous`.

### <a name="webhooks"></a>Веб-перехватчики

> [!NOTE]
> Режим веб-перехватчика доступен только для версии 1.x среды выполнения функций. Это изменение внесено для повышения производительности триггеров HTTP в версии 2.x.

В версии 1.x шаблоны веб-перехватчика обеспечивают дополнительную проверку полезных данных веб-перехватчика. В версии 2.x базовый HTTP-триггер по-прежнему работает и является рекомендуемым подходом для веб-перехватчиков. 

#### <a name="github-webhooks"></a>Веб-перехватчики GitHub

Чтобы настроить ответ на вызовы веб-перехватчика GitHub, прежде всего создайте функцию с триггером HTTP, для которого свойство **webHookType** будет иметь значение `github`. Затем скопируйте его URL-адрес и ключ API в репозиторий GitHub, используя страницу **Добавить веб-перехватчик**. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Веб-перехватчики Slack

Webhook Slack создает маркер автоматически и не позволяет вам задать его, поэтому необходимо настроить ключ для конкретной функции с использованием маркера, предоставленного Slack. Ознакомьтесь с разделом [Ключи авторизации](#authorization-keys).

### <a name="webhooks-and-keys"></a>Веб-перехватчики и ключи

Авторизация веб-перехватчика обрабатывается компонентом получателя веб-перехватчика, который входит в состав триггера HTTP. Механизм обработки различается в зависимости от типа веб-перехватчика. Каждый из этих механизмов зависит от ключей. По умолчанию используется ключ функции с именем default. Чтобы использовать другой ключ, необходимо настроить поставщик веб-перехватчика так, чтобы он отправлял имя ключа в составе запроса одним из следующих способов.

* **В строке запроса**: поставщик передает имя ключа в параметре `clientid` строки запроса, например `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **В заголовке запроса**: поставщик передает имя ключа в заголовке `x-functions-clientid`.

## <a name="trigger---limits"></a>Триггер — ограничения

Длина HTTP-запроса ограничена 100 МБ (104 857 600 байт), а длина URL-адреса — 4 КБ (4096 байт). Эти ограничения задает элемент `httpRuntime` [файла Web.config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config) среды выполнения.

Если функция, которая использует триггер HTTP, выполняется дольше 2,5 минут, будет превышено время ожидания шлюза и шлюз вернет ошибку HTTP 502. Функция продолжит работу, но вернуть ответ HTTP будет невозможно. Для долго выполняющихся функций рекомендуем следовать асинхронным шаблонам и возвращать расположение, в котором можно проверить состояние запроса. Чтобы узнать, как долго может выполняться функция, см. раздел [Масштабирование и размещение — план](functions-scale.md#timeout).

## <a name="output"></a>Выходные данные

Привязка для вывода HTTP используется для ответа отправителю запроса HTTP. Эта привязка требует наличия триггера HTTP и позволяет настроить ответ, возвращаемый на запрос этого триггера. Если привязка выходных данных HTTP не указана, в Функциях версии 1.x триггер HTTP возвращает ответ HTTP "200 — OK" с пустым текстом, а в Функциях версии 2.x — ответ "204 — содержимое отсутствует" с пустым текстом.

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json*. Для библиотек класса C# свойства атрибута, соответствующие этим свойствам *function.json*, отсутствуют.

|Свойство  |Описание  |
|---------|---------|
| **type** |Нужно задать значение `http`. |
| **direction** | Нужно задать значение `out`. |
| **name** | Имя переменной, используемое в коде функции для ответа, или `$return` для использования возвращаемого значения. |

## <a name="output---usage"></a>Использование выходной привязки

Чтобы отправить ответ HTTP, используйте шаблоны ответов языкового стандарта. В C# или скрипте C# задайте тип возвращаемого значения функции `IActionResult` или `Task<IActionResult>`. В C# атрибут возвращаемого значения не является обязательным.

Примеры ответов см. в разделе с [примером триггера](#trigger---example).

## <a name="hostjson-settings"></a>Параметры файла host.json

В этом разделе описываются глобальные параметры конфигурации, доступные для этой привязки в версии 2.x. В приведенном ниже примере файла host.json содержатся только параметры версии 2.x для этой привязки. Дополнительные сведения о глобальных параметрах конфигурации в версии 2.x см. в статье [Справочник по файлу host.json для Функций Azure](functions-host-json.md).

> [!NOTE]
> Сведения о файле host.json в Функциях Azure версии 1.x см. в [этой статье](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Свойство  |значение по умолчанию | Описание |
|---------|---------|---------| 
| customHeaders|Нет|Allows you to set custom headers in the HTTP response. The previous example adds the `X-Content-Type-Options` header to the response to avoid content type sniffing. |
|dynamicThrottlesEnabled|true<sup>\*</sup>|Если этот параметр включен, он заставляет конвейер обработки запросов периодически проверять счетчики производительности системы (подключений, потоков, процессов, памяти, ЦП и т. д.). При превышении встроенного порогового высокого значения (80 %) любого из этих счетчиков запросы будут отклоняться с ответом "429 — cлишком много запросов" до тех пор, пока счетчики не вернутся к нормальному уровню.<br/><sup>\*</sup>The default in a consumption plan is `true`. The default in a dedicated plan is `false`.|
|hsts|not enabled|When `isEnabled` is set to `true`, the [HTTP Strict Transport Security (HSTS) behavior of .NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) is enforced, as defined in the [`HstsOptions` class](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0). The above example also sets the [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) property to 10 days. Supported properties of `hsts` are: <table><tr><th>Свойство</th><th>Описание</th></tr><tr><td>excludedHosts</td><td>A string array of host names for which the HSTS header isn't added.</td></tr><tr><td>includeSubDomains</td><td>Boolean value that indicates whether the includeSubDomain parameter of the Strict-Transport-Security header is enabled.</td></tr><tr><td>maxAge</td><td>String that defines the max-age parameter of the Strict-Transport-Security header.</td></tr><tr><td>preload</td><td>Boolean that indicates whether the preload parameter of the Strict-Transport-Security header is enabled.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|The maximum number of http functions that are executed in parallel. Это позволяет регулировать параллелизм, что может помочь в управлении использованием ресурсов. Например, у вас может быть HTTP-функция, использующая много системных ресурсов (памяти, ЦП или сокетов) таким образом, что при слишком высоком параллелизме это вызывает проблемы. Или же функция может выполнять исходящие запросы к сторонней службе, и частоту таких вызовов необходимо ограничить. В таких случаях может помочь применение регулирования. <br/><sup>*</sup>The default for a consumption plan is 100. The default for a dedicated plan is unbounded (`-1`).|
|maxOutstandingRequests|200<sup>\*</sup>|Максимальное число невыполненных запросов, которое хранится в любой отдельно взятый момент времени. Это ограничение включает запросы, которые находятся в очереди, но еще не начали выполняться, а также все запросы в процессе выполнения. Все входящие запросы, превышающие это ограничение, отклоняются с ответом 429 "Too Busy" (Перегрузка). Это позволяет вызывающим объектам использовать стратегии повторов на основе времени, а также помогает вам контролировать максимальные задержки запросов. Эта настройка влияет только на очереди, которые создаются по пути выполнения средства обработки скриптов. Она не влияет на другие очереди, такие как очередь запросов ASP.NET. <br/><sup>\*</sup>\The default for a consumption plan is 200. The default for a dedicated plan is unbounded (`-1`).|
|routePrefix|api|Префикс маршрута, который применяется ко всем маршрутам. Используйте пустую строку, чтобы удалить префикс по умолчанию. |


## <a name="next-steps"></a>Дальнейшие действия

[Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
