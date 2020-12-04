---
title: Аутентификация в службах связи Azure
titleSuffix: An Azure Communication Services concept document
description: Узнайте о различных способах проверки подлинности приложения или службы в службах связи.
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 4d6e02852dcd2d30a764417a4b5e0e012a1d2ab5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571102"
---
# <a name="authenticate-to-azure-communication-services"></a>Аутентификация в службах связи Azure

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Эта статья содержит сведения о проверке подлинности клиентов в службах связи Azure с помощью *ключей доступа* и *маркеров доступа пользователей*. Все взаимодействия клиента со службами связи Azure должны пройти проверку подлинности.

В следующей таблице описаны параметры проверки подлинности, поддерживаемые клиентскими библиотеками служб связи Azure.

| Клиентская библиотека | Ключ доступа    | Маркеры доступа пользователей |
| -------------- | ------------- | ------------------ |
| Администрирование | Поддерживается     | Не поддерживается      |
| SMS            | Поддерживается     | Не поддерживается      |
| Чат           | Не поддерживается | Поддерживается          |
| Вызов        | Не поддерживается | Поддерживается          |

Каждый вариант авторизации кратко описан ниже.

- Проверка подлинности **ключа доступа** для SMS и административных операций. Проверка подлинности с помощью ключа доступа подходит для приложений, работающих в среде доверенной службы. Для проверки подлинности с помощью ключа доступа клиент создает [код проверки подлинности сообщения на основе хэша (HMAC)](https://en.wikipedia.org/wiki/HMAC) и включает его в `Authorization` заголовок каждого HTTP-запроса. Дополнительные сведения см. в разделе [Проверка подлинности с помощью ключа доступа](#authenticate-with-an-access-key).
- Проверка подлинности **маркера доступа пользователя** для разговора и вызова. Маркеры доступа пользователей позволяют клиентским приложениям проходить проверку подлинности непосредственно в службах связи Azure. Эти токены создаются в создаваемой службе подготовки маркеров на стороне сервера. Затем они предоставляются клиентским устройствам, которые используют маркер для инициализации разговора и вызова клиентских библиотек. Дополнительные сведения см. в разделе [Проверка подлинности с помощью маркера доступа пользователя](#authenticate-with-a-user-access-token).

## <a name="authenticate-with-an-access-key"></a>Проверка подлинности с помощью ключа доступа

Проверка подлинности ключа доступа использует общий секретный ключ для создания HMAC для каждого HTTP-запроса, вычисленного с помощью алгоритма SHA256, и отправляет его в `Authorization` заголовке с помощью `HMAC-SHA256` схемы.

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

Клиентские библиотеки служб связи Azure, использующие проверку подлинности ключа доступа, должны инициализироваться со строкой подключения вашего ресурса. Если клиентская библиотека не используется, можно программно создавать HMAC с помощью ключа доступа ресурса. Дополнительные сведения о строках подключения см. в [кратком руководстве по подготовке ресурсов](../quickstarts/create-communication-resource.md).

### <a name="sign-an-http-request"></a>Подписывание HTTP-запроса

Если вы не используете клиентскую библиотеку для выполнения HTTP-запросов к интерфейсам API службы связи Azure, вам потребуется программно создавать HMAC для каждого HTTP-запроса. Следующие шаги описывают создание заголовка авторизации.

1. Укажите отметку времени в формате UTC для запроса в `x-ms-date` заголовке или в стандартном `Date` заголовке HTTP. Служба проверяет это для защиты от определенных атак, в том числе атак с помощью воспроизведения.
1. Выполните хэширование текста HTTP-запроса с помощью алгоритма SHA256, а затем передайте его с запросом через `x-ms-content-sha256` заголовок.
1. Создайте строку для подписи путем сцепления глагола HTTP (например `GET` `PUT` , или), пути HTTP-запроса и значений `Date` `Host` `x-ms-content-sha256` HTTP-заголовков в следующем формате:
    ```
    VERB + "\n"
    URLPathAndQuery + "\n"
    DateHeaderValue + ";" + HostHeaderValue + ";" + ContentHashHeaderValue
    ```
1. Создайте сигнатуру HMAC-256 для строки в кодировке UTF-8, созданной на предыдущем шаге. Затем закодировать результаты в Base64. Обратите внимание, что вам также потребуется декодировать ключ доступа в формате Base64. Используйте следующий формат (показан в виде псевдокода):
    ```
    Signature=Base64(HMAC-SHA256(UTF8(StringToSign), Base64.decode(<your_access_key>)))
    ```
1. Укажите заголовок Authorization следующим образом:
    ```
    Authorization="HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"  
    ```
    Где `<hmac-sha256-signature>` — это код HMAC, вычисленный на предыдущем шаге.

## <a name="authenticate-with-a-user-access-token"></a>Проверка подлинности с помощью маркера доступа пользователя

Маркеры доступа пользователей позволяют клиентским приложениям проходить проверку подлинности непосредственно в службах связи Azure. Для этого необходимо настроить надежную службу, которая проверяет подлинность пользователей приложения и выдает маркеры доступа пользователей с помощью клиентской библиотеки администрирования. См. общую документацию по [архитектуре клиента и сервера](./client-and-server-architecture.md) , чтобы узнать больше о наших архитектурных вопросах.

`CommunicationUserCredential`Класс содержит логику предоставления учетных данных маркера доступа пользователя для клиентских библиотек и управления жизненным циклом.

### <a name="initialize-the-client-libraries"></a>Инициализация клиентских библиотек

Чтобы инициализировать клиентские библиотеки служб связи Azure, требующие проверки подлинности маркера доступа пользователя, сначала необходимо создать экземпляр `CommunicationUserCredential` класса, а затем использовать его для инициализации клиента API.

В следующих фрагментах кода показано, как инициализировать клиентскую библиотеку чата с помощью маркера доступа пользователя:

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
// user access tokens should be created by a trusted service using the Administration client library
var token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
var userCredential = new CommunicationUserCredential(token);

// initialize the chat client library with the credential
var chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// user access tokens should be created by a trusted service using the Administration client library
const token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance with the AzureCommunicationUserCredential class
const userCredential = new AzureCommunicationUserCredential(token);

// initialize the chat client library with the credential
let chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
// user access tokens should be created by a trusted service using the Administration client library
let token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
let userCredential = try CommunicationUserCredential(token: token)

// initialize the chat client library with the credential
let chatClient = try CommunicationChatClient(credential: userCredential, endpoint: ENDPOINT_URL)
```

#### <a name="java"></a>[Java](#tab/java)

```java
// user access tokens should be created by a trusted service using the Administration client library
String token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
CommunicationUserCredential userCredential = new CommunicationUserCredential(token);

// Initialize the chat client
final ChatClientBuilder builder = new ChatClientBuilder();
builder.endpoint(ENDPOINT_URL)
    .credential(userCredential)
    .httpClient(HTTP_CLIENT);
ChatClient chatClient = builder.buildClient();
```

---

### <a name="refreshing-user-access-tokens"></a>Обновление маркеров доступа пользователей

Маркеры доступа пользователей — это кратковременные учетные данные, которые необходимо повторно выдать, чтобы предотвратить сбои в работе служб. `CommunicationUserCredential`Конструктор принимает функцию обратного вызова обновления, которая позволяет обновлять маркеры доступа пользователей до истечения срока их действия. Этот обратный вызов следует использовать для получения нового маркера доступа пользователя из доверенной службы.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var userCredential = new CommunicationUserCredential(
    initialToken: token,
    refreshProactively: true,
    tokenRefresher: cancellationToken => fetchNewTokenForCurrentUser(cancellationToken)
);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const userCredential = new AzureCommunicationUserCredential({
  tokenRefresher: async () => fetchNewTokenForCurrentUser(),
  refreshProactively: true,
  initialToken: token
});
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
 let userCredential = try CommunicationUserCredential(initialToken: token, refreshProactively: true) { |completionHandler|
   let updatedToken = fetchTokenForCurrentUser()
   completionHandler(updatedToken, nil)
 }
```

#### <a name="java"></a>[Java](#tab/java)

```java
TokenRefresher tokenRefresher = new TokenRefresher() {
    @Override
    Future<String> getFetchTokenFuture() {
        return fetchNewTokenForCurrentUser();
    }
}

CommunicationUserCredential credential = new CommunicationUserCredential(tokenRefresher, token, true);
```
---

`refreshProactively`Параметр позволяет выбрать способ управления жизненным циклом маркера. По умолчанию, когда маркер устарел, обратный вызов блокирует запросы API и пытается обновить его. Если для параметра задано значение `refreshProactively` `true` обратного вызова, оно планируется и выполняется асинхронно до истечения срока действия маркера.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание маркеров доступа пользователей](../quickstarts/access-tokens.md)

Дополнительные сведения см. в следующих статьях:
- [Сведения об архитектуре клиент-сервер](../concepts/client-and-server-architecture.md)
