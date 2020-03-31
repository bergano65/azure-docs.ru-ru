---
title: Мгновенное конфиденциальное клиентское приложение (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как мгновенно использовать конфиденциальное клиентское приложение с вариантами конфигурации с помощью библиотеки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a520c5a1002e401f880fba84f8fc02a0a678133
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084736"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Мгновенное конфиденциальное клиентское приложение с вариантами конфигурации с использованием MSAL.NET

В этой статье описывается, как мгновенно использовать [конфиденциальное клиентское приложение](msal-client-applications.md) с помощью библиотеки подлинности Майкрософт для .NET (MSAL.NET).  Приложение мгновенно используется параметрами конфигурации, определенными в файле настроек.

Прежде чем инициализировать приложение, сначала необходимо [зарегистрировать](quickstart-register-app.md) его, чтобы ваше приложение было интегрировано с платформой идентификации Майкрософт. После регистрации вам может понадобиться следующая информация (которая может быть найдена на портале Azure):

- Идентификатор клиента (строка, представляющая GUID)
- URL-адрес поставщика идентификационных данных (названный экземпляр) и аудитория для вашего приложения. Эти два параметра коллективно известны как власть.
- Идентификатор клиента, если вы пишете строку бизнес-приложения исключительно для вашей организации (также названное приложением с одним арендатором).
- Секрет приложения (клиентская строка) или сертификат (типа X509Certificate2), если это конфиденциальное клиентское приложение.
- Для веб-приложений, а иногда и для приложений для публичных клиентов (в частности, когда вашему приложению необходимо использовать брокера), вы также установите redirectUri, где поставщик идентификационных данных свяжется с вашим приложением с маркерами безопасности.

## <a name="configure-the-application-from-the-config-file"></a>Настройка приложения из файла конфигурации
Название свойств опционов в MSAL.NET совпадает с именем свойств `AzureADOptions` in ASP.NET Core, поэтому вам не нужно писать клей код.

Конфигурация приложения ASP.NET Core описана в файле *appsettings.json:*

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

Начиная с MSAL.NET v3.x, вы можете настроить конфиденциальное клиентское приложение из файла конфигурации.

В классе, где требуется настроить и мгновенно настроить приложение, `ConfidentialClientApplicationOptions` необходимо объявить объект.  Привязать конфигурацию, прочитаную из источника (включая файл appconfig.json) `IConfigurationRoot.Bind()` к экземпляру вариантов приложения, используя метод из [пакета Nuget Microsoft.Extensions.Configuration.Binder:](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Это позволяет привязывать содержимое раздела "AzureAD" файла *appsettings.json* `ConfidentialClientApplicationOptions` к соответствующим свойствам объекта.  Далее построить `ConfidentialClientApplication` объект:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Добавление конфигурации времени выполнения
В конфиденциальном клиентском приложении обычно есть кэш на одного пользователя. Поэтому вам нужно будет получить кэш, связанный с пользователем и сообщить разработчику приложения, что вы хотите его использовать. Таким же образом, вы можете иметь динамически вычисленные перенаправить URI. В этом случае код следующий:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

