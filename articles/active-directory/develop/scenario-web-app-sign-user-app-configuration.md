---
title: Веб-приложение, которое поддерживает вход пользователей (Настройка кода) — платформе Microsoft identity
description: Узнайте, как создать веб-приложение, которое поддерживает вход пользователей (код конфигурации)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82e6cbcd01c87ddffb7eac8d0ea0faef85f41a13
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254009"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Веб-приложение, выполняет вход пользователей - код конфигурации

Узнайте, как настроить код для веб-приложения, где пользователи, впервые входящий в систему.

## <a name="libraries-used-to-protect-web-apps"></a>Библиотеки, которые используются для защиты веб-приложений

<!-- This section can be in an include for Web App and Web APIs -->
Ниже приведены библиотеки, используемые для защиты веб-приложения (и веб-API).

| платформа | Библиотека | Описание |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Расширения модели удостоверения для .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Расширения Microsoft Identity для .NET используется напрямую, ASP.NET и ASP.NET Core, и предлагает набор файлы DLL, запускаемые на .NET Framework и .NET Core. Из приложения веб-Core ASP.NET/ASP.NET, можно управлять с помощью проверки маркера **TokenValidationParameters** класса (в частности, в некоторых сценариях независимого поставщика программного обеспечения) |

## <a name="aspnet-core-configuration"></a>Конфигурации ASP.NET Core

### <a name="application-configuration-files"></a>Файлы конфигурации приложений

В ASP.NET Core, веб-приложения вход в систему пользователей с платформой Microsoft identity настраивается с помощью `appsettings.json` файл. Ниже перечислены параметры, которые необходимы для заполнения.

- облаке `Instance` Если вы хотите, чтобы ваше приложение для запуска в национальных облаках
- аудитории в `tenantId`
- `clientId` для вашего приложения, скопированный на портале Azure.

```JSon
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

В ASP.NET Core, уже имеется другой файл, содержащий URL-адрес (`applicationUrl`) и порт SSL (`sslPort`) для приложения, а также различные профили.

```JSon
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

На портале Azure, ответ URI, которые необходимо зарегистрировать в **проверки подлинности** странице вашего приложения должен соответствовать следующим URL-адресам; таким образом, для двух файлов конфигурации выше, они не `https://localhost:44321/signin-oidc` как applicationUrl — `http://localhost:3110` , но `sslPort` является указанным (44321) и `CallbackPath` — `/signin-oidc` как определено в `appsettings.json`.
  
Таким же образом, будет иметь значение выхода URI `https://localhost:44321/signout-callback-oidc`.

### <a name="initialization-code"></a>Код инициализации

В веб-приложений ASP.NET Core (и веб-API), код, выполнив инициализации приложения находится в `Startup.cs` файл, и явным образом, чтобы добавить проверку подлинности с помощью платформы (прежнее название — Azure AD) версии 2.0 Microsoft Identity, необходимо добавить следующий код. Комментарии в коде должно быть говорит само за себя.

  > [!NOTE]
  > При запуске проекта с по умолчанию и веб-проекта ASP.NET core в Visual studio или с помощью `dotnet new mvc` метод `AddAzureAD` доступна по умолчанию, так как связанные пакеты загружаются автоматически. Тем не менее если построить проект с нуля и пытаетесь использовать ниже кода мы рекомендуем добавить пакет NuGet **«Microsoft.AspNetCore.Authentication.AzureAD.UI»** в проект, чтобы сделать `AddAzureAD` из доступных методов.
  
```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft Identity Platform v2.0 endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Azure AD v2.0 endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>Конфигурация ASP.NET

В ASP.NET, приложение настраивается с помощью `Web.Config` файла

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

Код, связанные с проверкой подлинности, в веб-приложение ASP.NET или веб-API находится в `App_Start/Startup.Auth.cs` файл.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Входа систему и выхода](scenario-web-app-sign-user-sign-in.md)
