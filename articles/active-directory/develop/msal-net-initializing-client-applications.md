---
title: Инициализировать клиентские приложения (библиотека проверки подлинности Майкрософт для .NET) | Azure
description: Дополнительные сведения об инициализации общедоступного клиента и Конфиденциально клиентских приложений, использующих библиотеку проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6197d472bdfaf03c9f99baa7691354e735cc91e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075810"
---
# <a name="initialize-client-applications-using-msalnet"></a>Инициализировать клиентских приложений с помощью MSAL.NET
В этой статье описывается инициализация общедоступного клиента и Конфиденциально клиентских приложений, использующих библиотеку проверки подлинности Майкрософт для .NET (MSAL.NET).  Дополнительные сведения о типах клиентского приложения и параметры настройки приложения, [Обзор](msal-client-applications.md).

С помощью MSAL.NET 3.x, чтобы создать экземпляр приложения, рекомендуется с помощью создателей приложений: `PublicClientApplicationBuilder` и `ConfidentialClientApplicationBuilder`. Они предоставляют мощный механизм для настройки приложения из кода, или из файла конфигурации либо даже сочетание обоих подходов.

## <a name="prerequisites"></a>Технические условия
Перед инициализацией приложения, необходимо сначала [зарегистрировать](quickstart-register-app.md) таким образом, чтобы приложение можно интегрировать с платформой Microsoft identity.  После регистрации может потребоваться следующие сведения (которые можно найти на портале Azure):

- Идентификатор клиента (строка, представляющая GUID)
- URL-адрес поставщика удостоверений (с именем экземпляра) и аудитории входа для приложения. Эти два параметра, называются полномочия.
- Идентификатор клиента, при создании строки бизнес-приложений исключительно для вашей организации (также именованного приложения одним клиентом).
- Секрет приложения (строка секрета клиента) или сертификат (типа X509Certificate2) Если это приложение конфиденциального клиента.
- Для веб-приложений и иногда для общедоступных клиентских приложений (в частности, если ваше приложение должно использовать брокер) будет также установки redirectUri где поставщика удостоверений будет обращаться к задней приложения с помощью маркеров безопасности.

## <a name="ways-to-initialize-applications"></a>Способы инициализации приложений
Существует много различных способов создания клиентских приложений.

### <a name="initializing-a-public-client-application-from-code"></a>Инициализация общедоступного клиентского приложения из кода

Следующий код создает экземпляр общедоступного клиентского приложения, вход в систему пользователей в общедоступном облаке Microsoft Azure, с свою работу и учебных учетных записей или их личных учетных записей Майкрософт.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Инициализация конфиденциального клиентского приложения из кода

Таким же образом, следующий код создает экземпляр конфиденциальные приложения (веб-приложения, расположенный `https://myapp.azurewebsites.net`) обработка токенов от пользователей в общедоступном облаке Microsoft Azure, с свою работу и учебных учетных записей или их личных учетных записей Майкрософт. Приложение определяется с поставщиком удостоверений, отправляя секрет клиента:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Как правило, в рабочей среде, а не с помощью секрета клиента, можно использовать совместно с Azure AD сертификат. Код будет следующее:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Инициализация общедоступного клиентского приложения, параметры настройки

Следующий код создает экземпляр общедоступного клиентского приложения из объекта конфигурации, который может быть заполнены по умолчанию программным способом или чтение из файла конфигурации:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Инициализация конфиденциального клиентского приложения, параметры настройки

Одинаковые шаблон применяется к конфиденциального клиента приложения. Можно также добавить другие параметры, с помощью `.WithXXX` модификаторы (здесь сертификата).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Построитель модификаторы

Во фрагментах кода, с помощью создателей приложений, ряд `.With` методы могут применяться в качестве модификаторов (например, `.WithCertificate` и `.WithRedirectUri`). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Модификаторы, общие для общедоступных и Конфиденциально клиентских приложений

Модификаторы, которые можно задать на открытый клиент или конфиденциальный клиент построитель приложений являются:

|Параметр | ОПИСАНИЕ|
|--------- | --------- |
|`.WithAuthority()` 7 переопределения | Задает полномочия по умолчанию, приложение центра Azure AD, с возможностью выбора в облаке Azure, аудитории, клиента (идентификатор или домен имя клиента), или напрямую предоставляя URI центра.|
|`.WithAdfsAuthority(string)` | Задает приложение по умолчанию в качестве центра сертификации служб федерации Active Directory.|
|`.WithB2CAuthority(string)` | Задает приложение по умолчанию в качестве центра сертификации Azure AD B2C.|
|`.WithClientId(string)` | Переопределяет идентификатор клиента.|
|`.WithComponent(string)` | Задает имя библиотеки, с помощью MSAL.NET (по соображениям телеметрии). |
|`.WithDebugLoggingCallback()` | При вызове, приложение будет вызывать `Debug.Write` просто Включение отладочной трассировки. См. в разделе [ведение журнала](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) Дополнительные сведения.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Задайте параметры запроса уровня дополнительные приложения, которые отправляются во все запросы проверки подлинности. Это является переопределяемым на каждом уровне метод получения маркера (с тем же `.WithExtraQueryParameters pattern`).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Предоставляет дополнительные сценарии, такие как настройка для прокси-сервер HTTP, или заставить MSAL для использования конкретного HttpClient (например, в веб-приложения ASP.NET Core/API).|
|`.WithLogging()` | При вызове, приложение вызовет обратный вызов со отладочной трассировки. См. в разделе [ведение журнала](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) Дополнительные сведения.|
|`.WithRedirectUri(string redirectUri)` | Переопределяет URI перенаправления по умолчанию. В случае общедоступных клиентских приложений это будет полезно для сценариев, включающих компонент Service broker.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Задает делегат, используемый для отправки данных телеметрии.|
|`.WithTenantId(string tenantId)` | Переопределяет идентификатор клиента или описания клиента.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Модификаторы, используемые для приложений Xamarin.iOS

Модификаторы, которые можно задать на построитель приложение общедоступным клиентом на Xamarin.iOS:

|Параметр | ОПИСАНИЕ|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Xamarin.iOS только**: Задает группу безопасности iOS цепочке ключей (для сохраняемости кэша).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Модификаторы, используемые для конфиденциального клиента приложения

Ниже приведены модификаторов, которые можно установить на построитель конфиденциального клиента приложения.

|Параметр | ОПИСАНИЕ|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Задает сертификат идентификации приложения в Azure AD.|
|`.WithClientSecret(string clientSecret)` | Задает секрет клиента (пароль приложения), определение приложения в Azure AD.|

Эти модификаторы являются взаимоисключающими. Если вы укажете оба, MSAL вызовет значимое исключение.

### <a name="example-of-usage-of-modifiers"></a>Пример использования модификаторов

Предположим, что приложение является это бизнес-приложение, которое предназначено только для вашей организации.  Затем можно написать:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Где он становится интересных — теперь упростила программирования для национальных облаков. Если требуется приложения следует мультитенантное приложение в национальном облаке, можно написать, например:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Также имеется переопределение для служб федерации Active Directory (ADFS 2019 в настоящее время не поддерживается):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Наконец Если вы являетесь разработчиком Azure AD B2C, можно указать вашего клиента следующим образом:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
