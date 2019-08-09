---
title: Разработка и настройка приложений службы SignalR для функций Azure
description: Сведения о разработке и настройке безсерверных приложений в режиме реального времени с помощью функций Azure и службы SignalR Azure
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: be77704f562a1e05485e6f3704dff265635b1dc2
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882311"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Разработка и Настройка функций Azure с помощью службы Azure SignalR

Приложения функций Azure могут использовать [привязки службы Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) для добавления возможностей в режиме реального времени. Клиентские приложения используют клиентские пакеты SDK, доступные на нескольких языках, для подключения к службе Azure SignalR и получения сообщений в режиме реального времени.

В этой статье описываются основные понятия разработки и настройки приложения-функции Azure, интегрированного со службой SignalR.

## <a name="signalr-service-configuration"></a>Конфигурация службы SignalR

Службу Azure SignalR можно настроить в различных режимах. При использовании с функциями Azure служба должна быть настроена в бессерверном режиме.

На портал Azure выберите страницу *Параметры* для ресурса службы SignalR. Установите *режим обслуживания* "бессерверный".

![Режим Службы SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Разработка функций Azure

Для приложения в режиме реального времени, созданного с помощью функций Azure и службы SignalR Azure, обычно требуется две функции Azure:

* Функция Negotiate, которую клиент вызывает для получения действительного маркера доступа службы SignalR и URL-адреса конечной точки службы.
* Одна или несколько функций, которые отправляют сообщения или управляют членством в группе

### <a name="negotiate-function"></a>Функция Negotiate

Клиентскому приложению требуется допустимый маркер доступа для подключения к службе Azure SignalR. Маркер доступа может быть анонимным или прошедшим проверку подлинности по указанному ИДЕНТИФИКАТОРу пользователя. Для приложений службы SignalR без сервера требуется конечная точка HTTP с именем "Negotiate" для получения маркера и других сведений о соединении, таких как URL-адрес конечной точки службы SignalR.

Используйте функцию Azure, активируемую с помощью HTTP, и входную привязку *сигналрконнектионинфо* для создания объекта сведений о соединении. Функция должна иметь маршрут HTTP, который заканчивается на `/negotiate`.

Дополнительные сведения о создании функции Negotiate см. в справочнике по [входной привязке *сигналрконнектионинфо* ](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding).

Дополнительные сведения о создании маркера с проверкой подлинности см. в разделе [использование проверки подлинности службы приложений](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Отправка сообщений и управление членством в группах

Используйте выходную привязку *SignalR* для отправки сообщений клиентам, подключенным к службе Azure SignalR. Вы можете рассылать сообщения всем клиентам или отправить их подмножеству клиентов, которые прошли проверку подлинности с определенным ИДЕНТИФИКАТОРом пользователя или были добавлены в определенную группу.

Пользователи могут быть добавлены в одну или несколько групп. Вы также можете использовать выходную привязку *SignalR* для добавления или удаления пользователей в группы.

Дополнительные сведения см. в справочнике по [выходной привязке *SignalR* ](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding).

### <a name="signalr-hubs"></a>Концентраторы SignalR

SignalR имеет концепцию "концентраторов". Каждое клиентское подключение и каждое сообщение, отправленное из функций Azure, ограничены конкретным концентратором. Концентраторы можно использовать как способ разделения подключений и сообщений на логические пространства имен.

## <a name="client-development"></a>Разработка клиентов

Клиентские приложения SignalR могут использовать клиентский пакет SDK SignalR на одном из нескольких языков, чтобы легко подключаться и получать сообщения от службы Azure SignalR.

### <a name="configuring-a-client-connection"></a>Настройка подключения клиента

Чтобы подключиться к службе SignalR, клиент должен завершить успешное согласование подключения, которое состоит из следующих действий.

1. Выполните запрос к конечной точке *Negotiate* HTTP, описанной выше, чтобы получить допустимые сведения о соединении.
1. Подключение к службе SignalR с помощью URL-адреса конечной точки службы и маркера доступа , полученных из конечной точки согласования

Пакеты SDK клиента SignalR уже содержат логику, необходимую для выполнения подтверждения согласования. Передайте в `HubConnectionBuilder`пакет SDK URL-адрес конечной `negotiate` точки для согласования, за исключением сегмента. Ниже приведен пример в JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

По соглашению пакет SDK автоматически добавляет `/negotiate` к URL-адресу и использует его для начала согласования.

> [!NOTE]
> Если вы используете JavaScript/TypeScript SDK в браузере, необходимо [включить общий доступ к ресурсам на разных источниках (CORS)](#enabling-cors) на приложение-функция.

Дополнительные сведения о том, как использовать клиентский пакет SDK для SignalR, см. в документации по вашему языку:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Отправка сообщений от клиента службе

Хотя пакет SDK SignalR позволяет клиентским приложениям вызывать логику серверной части в концентраторе SignalR, эта функция пока не поддерживается при использовании службы SignalR с функциями Azure. Используйте HTTP-запросы для вызова функций Azure.

## <a name="azure-functions-configuration"></a>Настройка функций Azure

Приложения-функции Azure, которые интегрируются со службой Azure SignalR, можно развернуть как любое типичное приложение-функцию Azure с помощью таких методов, как [непрерывное развертывание](../azure-functions/functions-continuous-deployment.md), [ZIP-развертывание](../azure-functions/deployment-zip-push.md)и [Запуск из пакета](../azure-functions/run-functions-from-deployment-package.md).

Однако существует несколько особых соображений для приложений, использующих привязки службы SignalR. Если клиент выполняется в браузере, необходимо включить CORS. Если для приложения требуется проверка подлинности, можно интегрировать конечную точку Negotiate с проверкой подлинности службы приложений.

### <a name="enabling-cors"></a>Включение CORS

Клиент JavaScript/TypeScript выполняет HTTP-запросы к функции Negotiate, чтобы инициировать согласование подключения. Если клиентское приложение размещается в домене, отличном от домена приложения-функции Azure, необходимо включить общий доступ к ресурсам на основе источника (CORS) в приложении-функции или браузер будет блокировать запросы.

#### <a name="localhost"></a>Localhost

При запуске приложения-функции на локальном компьютере можно добавить `Host` раздел в *Local. Settings. JSON* , чтобы включить CORS. `Host` В разделе добавьте два свойства:

* `CORS`— Введите базовый URL-адрес, который является источником клиентского приложения.
* `CORSCredentials`-Задайте значение `true` , чтобы разрешить запросы "вискредентиалс"

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

#### <a name="cloud---azure-functions-cors"></a>Облако — функции Azure CORS

Чтобы включить CORS в приложении функции Azure, перейдите на экран конфигурации CORS на вкладке *функции платформы* приложения функции в портал Azure.

> [!NOTE]
> Конфигурация CORS пока недоступна в плане потребления Azure для функций Linux. Используйте службу [управления API Azure](#cloud---azure-api-management) для включения CORS.

Чтобы клиент SignalR вызывал функцию Negotiate, необходимо включить CORS с правами доступа-Control-Allow-Credential. Установите флажок, чтобы включить его.

В разделе *Разрешенные источники* добавьте запись с исходным URL-адресом исходной базы данных.

![Настройка CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Облако — управление API Azure

Служба управления API Azure предоставляет шлюз API, который добавляет возможности к существующим серверным службам. Его можно использовать для добавления CORS в приложение функции. Она предлагает уровень потребления с ценами оплаты за действие и ежемесячный бесплатный предоставление.

Сведения о том, как [импортировать приложение-функцию Azure](../api-management/import-function-app-as-api.md), см. в документации по управлению API. После импорта можно добавить политику входящего трафика, чтобы включить CORS с поддержкой Access-Control-Allow-Credentials.

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

Настройте клиенты SignalR для использования URL-адреса управления API.

### <a name="using-app-service-authentication"></a>Использование проверки подлинности службы приложений

Функции Azure имеют встроенную проверку подлинности, поддерживающую популярные поставщики, такие как Facebook, Twitter, учетная запись Майкрософт, Google и Azure Active Directory. Эта функция может быть интегрирована с привязкой *сигналрконнектионинфо* для создания подключений к службе Azure SignalR, которая была проверена на идентификатор пользователя. Приложение может отсылать сообщения с помощью выходной привязки *SignalR* , предназначенного для этого идентификатора пользователя.

В портал Azure на вкладке *функции платформы* приложения-функции откройте окно Параметры *проверки подлинности и авторизации* . Чтобы настроить проверку подлинности с помощью выбранного поставщика удостоверений, следуйте инструкциям в документации по [проверке подлинности службы приложений](../app-service/overview-authentication-authorization.md) .

После настройки прошедшие проверку подлинности HTTP `x-ms-client-principal-name` - `x-ms-client-principal-id` запросы будут включать заголовки, содержащие имя пользователя и идентификатор пользователя, прошедшего проверку подлинности, соответственно.

Эти заголовки можно использовать в конфигурации привязки *сигналрконнектионинфо* для создания подключений, прошедших проверку подлинности. Ниже приведен пример C# функции Negotiate, которая использует `x-ms-client-principal-id` заголовок.

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

Затем можно отправить сообщения этому пользователю, задав `UserId` свойство сообщения SignalR.

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

Сведения о других языках см. в справочнике по [привязкам службы Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) для функций Azure.

## <a name="next-steps"></a>Следующие шаги

В этой статье вы узнали, как разрабатывать и настраивать приложения службы SignalR для бессерверных приложений с помощью функций Azure. Попробуйте создать приложение самостоятельно с помощью одного из быстрых запусков или руководств на [странице обзора службы SignalR](index.yml).