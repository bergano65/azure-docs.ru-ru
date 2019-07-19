---
title: Веб-приложение, которое подписывает пользователей (конфигурация кода) — платформа Microsoft Identity
description: Узнайте, как создать веб-приложение, которое входит в систему пользователей (конфигурация кода)
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
ms.openlocfilehash: c962e95b3d213c4089b51f58139cab17a3332cbd
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853069"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Веб-приложение, которое входит в систему пользователей — конфигурация кода

Узнайте, как настроить код для веб-приложения, которое входит в систему пользователей.

## <a name="libraries-used-to-protect-web-apps"></a>Библиотеки, используемые для защиты веб-приложений

<!-- This section can be in an include for Web App and Web APIs -->
Библиотеки, используемые для защиты веб-приложения (и веб-API):

| Платформа | Библиотека | Описание |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Расширения модели удостоверений для .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Службы Microsoft Identity Extensions для .NET предлагают набор библиотек DLL, работающих как в .NET Framework, так и в .NET Core, непосредственно с помощью ASP.NET и ASP.NET Core. Из веб-приложения ASP.NET/ASP.NET Core можно управлять проверкой маркера с помощью класса **TokenValidationParameters** (в частности, в некоторых сценариях ISV). |

## <a name="aspnet-core-configuration"></a>Конфигурация ASP.NET Core

Фрагменты кода в этой статье и следующие извлекаются из пошагового [руководства по ASP.NET Core веб-приложению, главе 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg). Для получения подробных сведений о реализации может потребоваться обратиться к этому учебнику.

### <a name="application-configuration-files"></a>Файлы конфигурации приложения

В ASP.NET Core для входа в веб-приложение пользователей с платформой Microsoft Identity настраивается с помощью `appsettings.json` файла. Параметры, которые необходимо заполнить:

- облако `Instance` , если вы хотите, чтобы приложение запускалось в национальных облаках
- аудитория в`tenantId`
- `clientId` для приложения, скопированное из портал Azure.

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
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
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

В ASP.NET Core есть еще один файл, содержащий URL-адрес (`applicationUrl`) и порт SSL (`sslPort`) для приложения, а также различные профили.

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

В портал Azure идентификаторы URI ответа, которые необходимо зарегистрировать на странице **проверки** подлинности для вашего приложения, должны соответствовать этим URL-адресам. Таким образом, для двух файлов конфигурации, приведенных выше, они `https://localhost:44321/signin-oidc` бы были как `http://localhost:3110` applicationUrl, но `sslPort` задано значение (44321) `appsettings.json`, а `CallbackPath` — `/signin-oidc` как определено в.
  
Таким же образом URI выхода будет иметь значение `https://localhost:44321/signout-callback-oidc`.

### <a name="initialization-code"></a>Код инициализации

В ASP.NET Core веб-приложениях (и веб-API) код, выполняющий инициализацию приложения, находится `Startup.cs` в файле, и для добавления проверки подлинности с платформой Microsoft Identity (прежнее название — Azure AD) версии 2.0 необходимо добавить следующий код. Комментарии в коде должны быть описательными.

  > [!NOTE]
  > Если вы запускаете проект с веб-проектом ASP.NET Core по умолчанию в Visual `dotnet new mvc` Studio или `AddAzureAD` с помощью метода, он доступен по умолчанию, так как связанные пакеты автоматически загружаются. Однако если вы создаете проект с нуля и пытаетесь использовать приведенный ниже код, мы рекомендуем добавить пакет NuGet **Microsoft. AspNetCore. Authentication. AzureAD. UI** в проект, чтобы сделать этот `AddAzureAD` метод доступным.
  
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

В ASP.NET приложение настраивается с помощью `Web.Config` файла.

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

Код, связанный с проверкой подлинности в ASP.NET веб-приложениях и веб `App_Start/Startup.Auth.cs` -интерфейсах API, находится в файле.

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

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Вход и выход](scenario-web-app-sign-user-sign-in.md)
