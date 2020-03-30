---
title: Инициализация MSAL.NET клиентских приложений Azure
titleSuffix: Microsoft identity platform
description: Узнайте о инициализации публичных клиентских и конфиденциальных клиентских приложений, используя библиотеку подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 57ce6ab31421cd4016f7e204eeabce82f2f7e6a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083988"
---
# <a name="initialize-client-applications-using-msalnet"></a>Инициализация клиентских приложений с использованием MSAL.NET
В этой статье описывается инициализация публичных клиентских и конфиденциальных клиентских приложений с использованием библиотеки подлинности Майкрософт для .NET (MSAL.NET).  Чтобы узнать больше о типах клиентских приложений и вариантах конфигурации приложений, прочитайте [обзор.](msal-client-applications.md)

С MSAL.NET 3.x, рекомендуемый способ мгновенного приложения с помощью `PublicClientApplicationBuilder` `ConfidentialClientApplicationBuilder`приложений строителей: и . Они предлагают мощный механизм настройки приложения либо из кода, либо из файла конфигурации, или даже путем смешивания обоих подходов.

## <a name="prerequisites"></a>Предварительные требования
Прежде чем инициализировать приложение, сначала необходимо [зарегистрировать его,](quickstart-register-app.md) чтобы ваше приложение было интегрировано с платформой идентификации Майкрософт.  После регистрации вам может понадобиться следующая информация (которая может быть найдена на портале Azure):

- Идентификатор клиента (строка, представляющая GUID)
- URL-адрес поставщика идентификационных данных (названный экземпляр) и аудитория для вашего приложения. Эти два параметра коллективно известны как власть.
- Идентификатор клиента, если вы пишете строку бизнес-приложения исключительно для вашей организации (также названное приложением с одним арендатором).
- Секрет приложения (клиентская строка) или сертификат (типа X509Certificate2), если это конфиденциальное клиентское приложение.
- Для веб-приложений, а иногда и для приложений для публичных клиентов (в частности, когда вашему приложению необходимо использовать брокера), вы также установите redirectUri, где поставщик идентификационных данных свяжется с вашим приложением с маркерами безопасности.

## <a name="ways-to-initialize-applications"></a>Способы инициализации приложений
Есть много различных способов мгновенного клиентского приложения.

### <a name="initializing-a-public-client-application-from-code"></a>Инициализация публичного клиентского приложения из кода

Следующий код мгновенно приводит к общедоступному клиенту, пользователям, воданным в общедоступное облако Microsoft Azure, их рабочим и школьным учетным записям или их личным учетным записям Майкрософт.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Инициализация конфиденциального клиентского приложения из кода

Таким же образом, следующий код мгновенно выполняет конфиденциальное приложение `https://myapp.azurewebsites.net`(веб-приложение, расположенное по адресу) обработка токенов от пользователей в общедоступном облаке Microsoft Azure, с их рабочими и школьными учетными записями или их личными учетными записями Майкрософт. Приложение идентифицируется с поставщиком идентификационных данных путем обмена секретом клиента:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Как вы знаете, в производственной работе, вместо того, чтобы использовать секрет клиента, вы можете поделиться с Azure AD сертификатом. Код будет следующим:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Инициализация публичного клиентского приложения из параметров конфигурации

Следующий код мгновенно выполняет публичное клиентское приложение от объекта конфигурации, которое может быть заполнино в программно или прочитано из файла конфигурации:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Инициализация конфиденциального клиентского приложения из параметров конфигурации

Такая же схема применима и к конфиденциальным клиентским приложениям. Вы также можете добавить `.WithXXX` другие параметры с помощью модификаторов (здесь сертификат).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Модификаторы строителя

В фрагментах кода с использованием создателей приложений ряд методов `.With` может `.WithCertificate` быть `.WithRedirectUri`применен в качестве модификаторов (например, и). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Модификаторы, общие для публичных и конфиденциальных клиентских приложений

Модификаторы, которые можно установить на публичного клиента или конвейерное приложение клиента, :

|Модификатор | Описание|
|--------- | --------- |
|`.WithAuthority()`7 переопределения | Устанавливает полномочия приложения по умолчанию в орган Azure AD с возможностью выбора облака Azure, аудитории, арендатора (идентификатор арендатора или доменного имени) или предоставления непосредственно uri.|
|`.WithAdfsAuthority(string)` | Устанавливает полномочия приложения по умолчанию как полномочия ADFS.|
|`.WithB2CAuthority(string)` | Устанавливает полномочия приложения по умолчанию как полномочия Azure AD B2C.|
|`.WithClientId(string)` | Отменяет идентификатор клиента.|
|`.WithComponent(string)` | Устанавливает название библиотеки с использованием MSAL.NET (по причинам телеметрии). |
|`.WithDebugLoggingCallback()` | Если вызов вызывается, `Debug.Write` приложение вызовет просто включение отладки следов. Дополнительные сведения см. в статье, посвященной [ведению журналов](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging).|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Установите дополнительные параметры запроса уровня приложения, которые будут отправлены во всех запросах на аутентификацию. Это переизбавляемое на уровне каждого `.WithExtraQueryParameters pattern`метода приобретения токенов (с тем же).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Позволяет использовать расширенные сценарии, такие как настройка прокси HTTP, или принуждение MSAL к использованию определенного httpClient (например, в ASP.NET основных веб-приложений/AI).|
|`.WithLogging()` | При вызове приложение вызовет обратный вызов с отладкой. Дополнительные сведения см. в статье, посвященной [ведению журналов](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging).|
|`.WithRedirectUri(string redirectUri)` | Перенаправляет URI по умолчанию. В случае публичных клиентских приложений это будет полезно для сценариев с участием брокера.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Устанавливает делегата, используемого для отправки телеметрии.|
|`.WithTenantId(string tenantId)` | Отменяет идентификатор клиента или описание клиента.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Модификаторы, специфичныдля для приложений Xamarin.iOS

Модификаторы, которые можно установить на общедоступном клиентском приложении на Xamarin.iOS:

|Модификатор | Описание|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Xamarin.iOS только:** Устанавливает группу безопасности ключевой цепи iOS (для сохранения кэша).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Модификаторы, специфичные для конфиденциальных клиентских приложений

Модификаторы, которые можно установить на разработчике конфиденциальных клиентских приложений:

|Модификатор | Описание|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Устанавливает сертификат, идентифицирующий приложение, с помощью Azure AD.|
|`.WithClientSecret(string clientSecret)` | Устанавливает секрет клиента (пароль приложения), идентифицирующий приложение с помощью Azure AD.|

Эти модификаторы являются взаимоисключающими. Если вы предоставите оба, MSAL будет бросать значимое исключение.

### <a name="example-of-usage-of-modifiers"></a>Пример использования модификаторов

Предположим, что ваше приложение является приложением line-of-business, которое предназначено только для вашей организации.  Тогда вы можете написать:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Там, где становится интересно, что программирование для национальных облаков в настоящее время упрощены. Если вы хотите, чтобы ваше приложение было мультитенантным приложением в национальном облаке, вы можете написать, например:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Существует также переопределение для ADFS (ADFS 2019 в настоящее время не поддерживается):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Наконец, если вы являетесь разработчиком Azure AD B2C, вы можете указать арендатора следующим образом:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
