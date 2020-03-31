---
title: Привязка ввода службы СигналЕр функций Azure функций
description: Научитесь возвращать URL-адрес конечных точек службы SignalR и токен доступа в Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 53d336aff3177a76c5e02266ffb8484bd9945119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530267"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Привязка ввода службы SignalR для функций Azure

Прежде чем клиент сможет подключиться к службе Azure SignalR, необходимо получить URL-адрес конечной точки службы и действительный маркер доступа. Входная привязка *SignalRConnectionInfo* создает URL-адрес конечной точки службы SignalR и допустимый маркер, которые используются для подключения к службе. Так как маркер ограничен по времени и может использоваться для идентификации конкретного пользователя при подключении, не следует его кэшировать или передавать в совместное пользование между клиентами. С помощью этой привязки клиенты могут использовать триггер HTTP для получения сведений о подключении.

Для получения дополнительной информации о том, как эта привязка используется для создания функции «переговоры», которая может быть использована клиентом SignalR SDK, см. [Azure Functions development and configuration article](../azure-signalr/signalr-concept-serverless-development-config.md)

Для получения информации о настройке и деталях конфигурации, [см.](functions-bindings-signalr-service.md)

## <a name="example"></a>Пример

# <a name="c"></a>[C #](#tab/csharp)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), получающая сведения о подключении SignalR с помощью входной привязки и возвращающая их по протоколу HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

В следующем примере показана привязка ввода информации о входе связи signalR в файле *function.json* и [функция С- Скрипт,](functions-reference-csharp.md) использующая привязку для возврата информации о подключении.

Данные привязки в файле *function.json*:

Пример файла function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Вот код сценария C':

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

В следующем примере показана входная привязка для сведений о подключении SignalR в файле *function.json* и [функция JavaScript](functions-reference-node.md), использующая привязку для возврата сведений о подключении.

Данные привязки в файле *function.json*:

Пример файла function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Ниже показан код JavaScript.

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

В следующем примере показана привязка ввода информации о входе связи соединения SignalR в файле *function.json* и [функция Python,](functions-reference-python.md) использующая привязку для возврата информации о подключении.

Данные привязки в файле *function.json*:

Пример файла function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Ниже приведен код Python.

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

В следующем примере показана [функция Java,](functions-reference-java.md) которая приобретает информацию о подключении SignalR с помощью связывания ввода и возвращает ее через HTTP.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="authenticated-tokens"></a>Прошедшие проверку подлинности маркеры

Если функцию активирует прошедший проверку подлинности клиент, вы можете добавить утверждение идентификатора пользователя для созданного маркера. Вы можете легко добавить аутентификацию в функциональное приложение с помощью [аутентификации Службы App.](../app-service/overview-authentication-authorization.md)

Проверка подлинности службы приложений задает заголовки HTTP `x-ms-client-principal-id` и `x-ms-client-principal-name`, содержащие имя и идентификатор субъекта клиента прошедшего проверку подлинности пользователя соответственно.

# <a name="c"></a>[C #](#tab/csharp)

Вы можете `UserId` установить свойство привязки к значению `{headers.x-ms-client-principal-id}` из `{headers.x-ms-client-principal-name}`заголовка с помощью [связывающего выражения](./functions-bindings-expressions-patterns.md): или .

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Вы можете `userId` установить свойство привязки к значению `{headers.x-ms-client-principal-id}` из `{headers.x-ms-client-principal-name}`заголовка с помощью [связывающего выражения](./functions-bindings-expressions-patterns.md): или .

Пример файла function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Вот код сценария C':

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Вы можете `userId` установить свойство привязки к значению `{headers.x-ms-client-principal-id}` из `{headers.x-ms-client-principal-name}`заголовка с помощью [связывающего выражения](./functions-bindings-expressions-patterns.md): или .

Пример файла function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Ниже показан код JavaScript.

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

Вы можете `userId` установить свойство привязки к значению `{headers.x-ms-client-principal-id}` из `{headers.x-ms-client-principal-name}`заголовка с помощью [связывающего выражения](./functions-bindings-expressions-patterns.md): или .

Пример файла function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Ниже приведен код Python.

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

Вы можете `userId` установить свойство привязки к значению `{headers.x-ms-client-principal-id}` из `{headers.x-ms-client-principal-name}`заголовка с помощью [связывающего выражения](./functions-bindings-expressions-patterns.md): или .

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="next-steps"></a>Дальнейшие действия

- [Отправка сообщений службы SignalR (связывание выходов)](./functions-bindings-signalr-service-output.md) 
