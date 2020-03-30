---
title: Разработка & настройки приложения Azure Functions - Azure SignalR
description: Подробная информация о том, как разрабатывать и настраивать приложения без серверов в режиме реального времени с помощью функций Azure и службы Azure SignalR
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: e1157a695d34c75b237391427b37365421366ef8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523176"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure Functions development and configuration with Azure SignalR Service (Разработка и настройка функций Azure с помощью Службы Azure SignalR)

Приложения Azure Functions могут использовать [привязки службы Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) для добавления возможностей в реальном времени. Клиентские приложения используют клиентские SDK, доступные на нескольких языках, для подключения к службе Azure SignalR и получения сообщений в режиме реального времени.

В этой статье описаны концепции разработки и настройки приложения Azure Function, интегрированного с службой SignalR.

## <a name="signalr-service-configuration"></a>Конфигурация службы SignalR

Служба СигналR Azure может быть настроена в разных режимах. При использовании функций Azure служба должна быть настроена в *режиме Без сервера.*

На портале Azure найдите страницу *«Настройки»* ресурса службы SignalR. Установите *режим обслуживания* на без *сервера.*

![Режим Службы SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Разработка функций Azure

Для бессерверного приложения, работающего в реальном времени и созданного с помощью Функций Azure и Службы Azure SignalR, обычно требуется две функции Azure:

* функция negotiate, которую клиент вызывает, чтобы получить действительный маркер доступа Службы SignalR и URL-адрес конечной точки службы;
* одна или несколько функций для отправки сообщений или управления членством в группе.

### <a name="negotiate-function"></a>переговоры функции

Клиенту требуется действительный токен доступа для подключения к службе Azure SignalR. Токен доступа может быть анонимным или аутентифицированным к данному идентификатору пользователя. Приложения службы без сервера SignalR требуют, чтобы конечная точка HTTP была названа «переговоры» для получения токена и другой информации о подключении, такой как URL-адрес endpoint службы SignalR.

Используйте функцию срабатывания azure HTTP и привязку ввода *SignalRConnectionInfo* для генерации информационного объекта соединения. Функция должна иметь маршрут HTTP, `/negotiate`который заканчивается в .

Для получения дополнительной информации о том, [ *SignalRConnectionInfo* ](../azure-functions/functions-bindings-signalr-service-input.md)как создать функцию переговоров, см.

Чтобы узнать о том, как создать токен с подлинностью, обратитесь к [с помощью аутентификации службы приложений.](#using-app-service-authentication)

### <a name="sending-messages-and-managing-group-membership"></a>Отправка сообщений и управление членством в группе

Используйте привязку вывода *SignalR* для отправки сообщений клиентам, подключенным к службе Azure SignalR. Вы можете передавать сообщения всем клиентам, или вы можете отправить их подмноженый набор клиентов, которые проверены с конкретным идентификатором пользователя или были добавлены в конкретную группу.

Пользователи могут быть добавлены в одну или несколько групп. Вы также можете использовать привязку вывода *SignalR* для добавления или удаления пользователей в/из групп.

Для получения дополнительной [ *SignalR* ](../azure-functions/functions-bindings-signalr-service-output.md)информации см.

### <a name="signalr-hubs"></a>Концентраторы SignalR

SignalR имеет понятие "хабы". Каждое соединение клиента и каждое сообщение, отправленное из функций Azure, приковано к определенному концентратору. Концентраторы можно использовать как способ разделения подключений и сообщений на логические пространства имен.

## <a name="client-development"></a>Разработка клиентских приложений

Клиентские приложения SignalR могут использовать клиентский SDK SignalR на одном из нескольких языков для легкого подключения к сообщениям Azure SignalR и получения их.

### <a name="configuring-a-client-connection"></a>Настройка клиентского соединения

Чтобы подключиться к службе SignalR, клиент должен завершить успешное согласование соединения, которое состоит из следующих шагов:

1. Сделать запрос на *переговоры* HTTP конечная точка обсуждается выше, чтобы получить достоверные сведения о связи
1. Подключение к службе SignalR с помощью URL-адреса и токендоступа доступа, полученного из конечной точки *переговоров*

SDK клиента SignalR уже содержат логику, необходимую для выполнения переговорного рукопожатия. Передайте URL-адрес конечной точки `negotiate` переговоров, за вычетом `HubConnectionBuilder`сегмента, в SDK. Вот пример javaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

По конвенции SDK автоматически `/negotiate` прижимает URL и использует его для начала переговоров.

> [!NOTE]
> Если вы используете JavaScript/TypeScript SDK в браузере, необходимо [включить совместное использование ресурсов поперечному происхождению (CORS)](#enabling-cors) в приложении функции.

Для получения дополнительной информации о том, как использовать клиент SignalR SDK, обратитесь к документации для вашего языка:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [Javascript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Отправка сообщений от клиента в службу

Хотя SignalR SDK позволяет клиентским приложениям вызывать логику бэкэнда в концентраторе SignalR, эта функциональность еще не поддерживается при использовании службы SignalR с функциями Azure. Используйте запросы HTTP для использования функций Azure.

## <a name="azure-functions-configuration"></a>Конфигурация функций Azure

Приложения Azure Function, которые интегрируются с службой Azure SignalR, могут быть развернуты, как и любое типичное приложение Azure Function, используя такие методы, как [непрерывное развертывание,](../azure-functions/functions-continuous-deployment.md) [развертывание почтовых отправлений](../azure-functions/deployment-zip-push.md)и [запуск из пакета.](../azure-functions/run-functions-from-deployment-package.md)

Тем не менее, есть несколько специальных соображений для приложений, которые используют привязки службы SignalR. Если клиент работает в браузере, CORS должен быть включен. И если приложение требует аутентификации, вы можете интегрировать конечную точку переговоров с аутентификацией службы приложений.

### <a name="enabling-cors"></a>Включение CORS

Клиент JavaScript/TypeScript делает HTTP запросы на переговорную функцию, чтобы инициировать переговоры о подключении. Когда клиентское приложение размещается в другом домене, чем приложение Azure Function, совместное использование ресурсов кросс-происхождения (CORS) должно быть включено в приложении Функция или браузер будет блокировать запросы.

#### <a name="localhost"></a>Localhost

При запуске приложения Функция на локальном `Host` компьютере можно добавить раздел на *local.settings.json* для включения CORS. В `Host` разделе добавьте два свойства:

* `CORS`- введите базовый URL-адрес, который является источником клиентского приложения
* `CORSCredentials`- установить `true` его, чтобы "с полномочиями" запросы

Пример

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="cloud---azure-functions-cors"></a>Облако - Функции Azure CORS

Чтобы включить CORS в приложении Azure Function, перейдите на экран конфигурации CORS под вкладкой *функций платформы* приложения функции вашего приложения Функции на портале Azure.

> [!NOTE]
> Конфигурация CORS пока не доступна в плане потребления Linux Azure Functions. Используйте [управление API Azure](#cloud---azure-api-management) для включения CORS.

CORS с функциями Control-Control-Allow-Credentials должны быть включены для клиента SignalR для вызова функции переговоров. Выберите флажок, чтобы включить его.

В разделе *Разрешенное происхождение* добавьте запись с исходным URL-адресом вашего веб-приложения.

![Настройка CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Облако - Управление API Azure

Azure API Management предоставляет шлюз API, который добавляет возможности существующим бэк-энд-службам. Вы можете использовать его для добавления CORS в приложение функции. Он предлагает уровень потребления с платой за действие ценообразования и ежемесячный бесплатный грант.

Обратитесь к документации Управления API для получения информации о том, как [импортировать приложение Azure Function.](../api-management/import-function-app-as-api.md) После импорта можно добавить входящий политики, чтобы включить CORS с поддержкой Access-Control-Allow-Credentials.

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

Наверстойте клиентов SignalR для использования URL-адреса управления API.

### <a name="using-app-service-authentication"></a>Использование аутентификации службы приложений

Azure Functions имеет встроенную аутентификацию, поддерживая популярных провайдеров, таких как Facebook, Twitter, Учетная запись Майкрософт, Google и Активный каталог Azure. Эта функция может быть интегрирована с привязкой *SignalRConnectionInfo* для создания подключений к службе Azure SignalR, которые были проверены на идентификатор пользователя. Приложение может отправлять сообщения с помощью привязки *вывода SignalR,* которые предназначены для этого идентификатора пользователя.

На портале Azure, на вкладке *платформы приложения* функция функции приложения функции, откройте окно настроек *аутентификации/авторизации.* Следуйте документации для [проверки подлинности службы приложений](../app-service/overview-authentication-authorization.md) для настройки аутентификации с помощью поставщика идентификационных данных по вашему выбору.

После настройки аутентифицированные `x-ms-client-principal-name` `x-ms-client-principal-id` запросы HTTP будут включать и заголовки, содержащие имя пользователя и идентификатор пользователя, удостоверяющий личность, соответственно.

Эти заголовки можно использовать в конфигурации связывания *SignalRConnectionInfo* для создания аутентифицированных соединений. Вот пример функции переговоров по `x-ms-client-principal-id` C', которая использует заголовок.

```csharp
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

Затем вы можете отправлять сообщения этому `UserId` пользователю, установив свойство сообщения SignalR.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

Для получения информации о других языках смотрите [привязки службы Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) для ссылки Azure Functions.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как разрабатывать и настраивать серверные приложения службы SignalR с помощью функций Azure. Попробуйте создать приложение самостоятельно, используя один из быстрых стартов или учебников на [странице обзора службы SignalR.](index.yml)