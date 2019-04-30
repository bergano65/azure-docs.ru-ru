---
title: Разработка и настройка приложений служба "функции Azure" в SignalR
description: Сведения о разработке и настройке бессерверных приложений в режиме реального времени, с помощью функций Azure и служба Azure SignalR
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: 9b68b9d0bbac984c29759cf4b7b026a559a9d819
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808999"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure разработки функций и конфигурации с помощью службы Azure SignalR

Приложения функций Azure можно использовать [привязки служба Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) для добавления возможностей в режиме реального времени. Клиентские приложения используют клиентские пакеты SDK доступен на нескольких языках для подключения к службе Azure SignalR и получать сообщения в режиме реального времени.

В этой статье описываются основные понятия для разработки и настройки приложение-функцию Azure, интегрированного со службой SignalR.

## <a name="signalr-service-configuration"></a>Конфигурация службы SignalR

Служба Azure SignalR можно настроить в разных режимах. При использовании с функциями Azure, служба должна быть настроена в *независимая* режим.

На портале Azure найдите *параметры* страницу ресурса службы SignalR. Задайте *режим службы* для *независимая*.

![Режим Службы SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Разработка функций Azure

Архитектура бессерверных приложений в режиме реального времени, созданных с помощью функций Azure и служба Azure SignalR, обычно требуется две функции Azure:

* Функция «согласование», клиент вызывает для получения допустимых службы SignalR маркер доступа и URL-адрес конечной точки службы
* Одна или несколько функций, которые отправляют сообщения или управлять членством в группах

### <a name="negotiate-function"></a>функция согласования

Клиентскому приложению требуется действительный маркер доступа для подключения к службе Azure SignalR. Маркер доступа может быть анонимных или прошедших проверку подлинности, идентификатора данного пользователя. Бессерверные приложения служба SignalR требуют конечную точку HTTP с именем «согласование» для получения токена и другие сведения, такие как URL-адрес конечной точки службы SignalR.

Azure функции, активируемой использование HTTP и *SignalRConnectionInfo* входной привязки для создания объекта сведений о соединении. Функция должна иметь HTTP-маршрут, который заканчивается на `/negotiate`.

Дополнительные сведения о том, как создать функцию negotiate см. в разделе [ *SignalRConnectionInfo* ввода ссылки привязки](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding).

Дополнительные сведения о том, как создать токен с проверкой подлинности, см. [проверку подлинности службы приложений с помощью](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Отправка сообщений и управление членством в группе

Используйте *SignalR* выходной привязки для отправки сообщений клиентам подключен к службе Azure SignalR. Можно выполнить рассылку сообщений всем клиентам, или вы можете отправить им в подмножество клиентов, которые проходят проверку подлинности с Идентификатором определенного пользователя или были добавлены к определенной группе.

Пользователи могут быть добавлены в одну или несколько групп. Можно также использовать *SignalR* выходная привязка для добавления или удаления пользователей из групп и.

Дополнительные сведения см. в разделе [ *SignalR* привязки ссылки на выходные данные](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding).

### <a name="signalr-hubs"></a>Концентраторы SignalR

SignalR существует понятие «концентраторы». Каждое клиентское подключение и каждое сообщение, отправленное из функций Azure относится к конкретному концентратору. Можно использовать концентраторы как способ отделения подключений и сообщения в логических пространств имен.

## <a name="client-development"></a>Разработка клиентских приложений

SignalR клиентских приложений могут использовать пакет SDK в одном из нескольких языков легко подключиться к и получать сообщения из службы Azure SignalR клиента SignalR.

### <a name="configuring-a-client-connection"></a>Настройка подключения клиента

Для подключения к службе SignalR клиента необходимо выполнить согласование успешного подключения, которая состоит из следующих действий:

1. Отправить запрос на *согласования* конечную точку HTTP, описанный выше, для получения сведений о соединении
1. Подключиться к службе SignalR с помощью URL-адрес конечной точки службы и маркер доступа получен из *согласования* конечной точки

Клиентские пакеты SDK SignalR уже содержать логику, необходимую для выполнения согласования подтверждения соединения. Передать URL-адрес конечной точки negotiate, за вычетом `negotiate` сегмент, в пакет SDK `HubConnectionBuilder`. Ниже приведен пример на языке JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

По соглашению, пакет SDK автоматически добавляет `/negotiate` URL-адрес и использует его для начала процесса согласования.

> [!NOTE]
> Если вы используете пакет SDK JavaScript/TypeScript в браузере, необходимо [включить кросс-совместного использования ресурсов (CORS)](#enabling-cors) на свое приложение-функцию.

Дополнительные сведения о том, как использовать пакет SDK клиента SignalR см. в документации для вашего языка:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Отправка сообщений от клиента к службе

Несмотря на то, что пакет SDK SignalR позволяет клиентским приложениям вызывать логику серверной части в концентратор SignalR, эта функция не еще поддерживается при использовании службы SignalR с помощью функций Azure. Используйте HTTP-запросы для вызова функций Azure.

## <a name="azure-functions-configuration"></a>Настройка функций Azure

Приложения-функции Azure, которые интегрируются со службой Azure SignalR может быть развернута как любой типичный приложения-функции Azure, с помощью методов, таких как [постоянно развертывания](../azure-functions/functions-continuous-deployment.md), [zip развертывания](../azure-functions/deployment-zip-push.md)и [выполнения из пакета](../azure-functions/run-functions-from-deployment-package.md).

Тем не менее существует ряд особые рекомендации для приложений, использующих привязки служб SignalR. Если клиент работает в браузере, необходимо включить CORS. Если приложение требует проверки подлинности, вы также можете интегрировать negotiate конечной точки с проверкой подлинности службы приложений.

### <a name="enabling-cors"></a>Включение CORS

JavaScript/TypeScript клиент отправляет HTTP-запросов в функцию negotiate, чтобы инициировать установления соединения. Если клиентское приложение размещено в разных доменах приложения-функции Azure, браузер блокирует запросы или кросс-совместного использования ресурсов (CORS) нужно включить в приложении-функции.

#### <a name="localhost"></a>localhost

При запуске приложения-функции на локальном компьютере, можно добавить `Host` раздел *local.settings.json* для включения CORS. В `Host` добавьте два свойства:

* `CORS` -Введите базовый URL-адрес, который является источником клиентское приложение
* `CORSCredentials` -Задайте для него значение `true` для разрешения запросов «withCredentials»

Пример:

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

#### <a name="azure"></a>Таблицы Azure

Чтобы включить CORS в приложение-функцию Azure, перейдите на экране настройки CORS в разделе *функции платформы* вкладке приложения-функции на портале Azure.

Необходимо включить CORS элемент управления-разрешить-учетные данные для доступа клиента SignalR для вызова функции negotiate. Установите флажок, чтобы включить его.

В *разрешенные источники* добавьте запись с исходный базовый URL-адрес веб-приложения.

![Настройка CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

### <a name="using-app-service-authentication"></a>С помощью проверки подлинности службы приложений

Функции Azure имеет встроенные проверки подлинности, поддержка популярных поставщиков, таких как Facebook, Twitter, учетная запись Майкрософт, Google и Azure Active Directory. Эта функция может интегрироваться с *SignalRConnectionInfo* привязки для создания подключений к службе Azure SignalR, которые прошли проверку подлинности для идентификатора пользователя. Приложение может отправлять сообщения с помощью *SignalR* выходной привязки, которые предназначены для этого идентификатора пользователя.

На портале Azure, в приложения-функции *функции платформы* открытой вкладкой ", *проверки подлинности и авторизации* окно" Параметры ". Следуя инструкциям в документации для [проверку подлинности службы приложений](../app-service/overview-authentication-authorization.md) для настройки проверки подлинности, используя поставщик удостоверений по своему усмотрению.

После настройки проверенных HTTP-запросов будет включать `x-ms-client-principal-name` и `x-ms-client-principal-id` заголовков, содержащих проверку подлинности имени пользователя и идентификатора пользователя, соответственно.

Можно использовать эти заголовки в вашей *SignalRConnectionInfo* конфигурацию привязки для создания подключений с проверкой подлинности. Вот пример C# согласования функцию, которая использует `x-ms-client-principal-id` заголовка.

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

Затем можно отправлять сообщения для одного пользователя, задав `UserId` свойство сообщения SignalR.

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

Дополнительные сведения о других языках, см. в разделе [привязки служба Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) для ссылки на функции Azure.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как разработать и настроить бессерверных приложений служба SignalR с помощью функций Azure. Попробуйте создать приложение самостоятельно, используя один из кратких руководств и учебников на [страницу обзора в SignalR](index.yml).