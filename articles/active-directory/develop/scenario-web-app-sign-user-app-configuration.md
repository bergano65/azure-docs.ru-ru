---
title: Назначаем веб-приложение, которое подписывается в пользователях - платформа идентификации Microsoft Azure
description: Узнайте, как создать веб-приложение, которое подписывается в пользователях (конфигурация кода)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 3de1edc8560cfc85f52293c095fa824b364d2058
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881644"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Веб-приложение, которое подписывает в пользователях: Конфигурация кода

Узнайте, как настроить код для веб-приложения, которое подписывается в пользователях.

## <a name="libraries-for-protecting-web-apps"></a>Библиотеки для защиты веб-приложений

<!-- This section can be in an include for Web App and Web APIs -->
Библиотеки, используемые для защиты веб-приложения (и веб-API), являются:

| Платформа | Библиотека | Описание |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Расширение модели идентификации для .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Используется непосредственно ASP.NET и ASP.NET Core, Microsoft Identity Model Extensions для .NET предлагает набор DLLs, работающих как на .NET Framework, так и на .NET Core. Из веб-приложения ASP.NET или ASP.NET Core можно управлять проверкой маркеров с помощью класса **TokenValidationParameters** (в частности, в некоторых сценариях партнеров). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Поддержка веб-приложений Java |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Поддержка веб-приложений Python |

Выберите вкладку, соответствующую интересуейной платформе:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Фрагменты кода в этой статье и следующие извлекаются из [ASP.NET Core веб-приложение инкрементный учебник, глава 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Вы можете сослаться на этот учебник для полной информации о реализации.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Фрагменты кода в этой статье и следующие материалы извлекаются из [ASP.NET образца веб-приложения.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)

Возможно, вы захотите сослаться на этот пример для получения полной информации о реализации.

# <a name="java"></a>[Java](#tab/java)

Фрагменты кода в этой статье и следующие материалы извлекаются из [веб-приложения Java, вызывая](https://github.com/Azure-Samples/ms-identity-java-webapp) образец графика Microsoft в MSAL Java.

Возможно, вы захотите сослаться на этот пример для получения полной информации о реализации.

# <a name="python"></a>[Python](#tab/python)

Фрагменты кода в этой статье и следующие материалы извлекаются из [веб-приложения Python, вызывая](https://github.com/Azure-Samples/ms-identity-python-webapp) образец графика Microsoft в MSAL Python.

Возможно, вы захотите сослаться на этот пример для получения полной информации о реализации.

---

## <a name="configuration-files"></a>Файлы конфигурации

Веб-приложения, которые регистрируются в пользователях с помощью платформы идентификации Майкрософт, обычно настраиваются с помощью файлов конфигурации. Настройки, которые необходимо заполнить:

- Экземпляр облака`Instance`(), если вы хотите, чтобы ваше приложение запускалось в национальных облаках, например
- Аудитория в идентификаторе арендатора ()`TenantId`
- Идентификатор клиента ()`ClientId`для вашего приложения, скопированный с портала Azure

Иногда, приложения могут быть параметризированы, `Authority`который является `Instance` `TenantId`конкатенацией и .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

В ASP.NET Core эти настройки расположены в файле [appsettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) в разделе "AzureAd".

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

В ASP.NET Core, другой файл[(свойства »launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) содержит URL (`applicationUrl`)`sslPort`и tLS / SSL порт ( ) для вашего приложения и различных профилей.

```Json
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

На портале Azure, ответы URIs, что вам нужно зарегистрироваться на странице **аутентификации** для приложения должны соответствовать этим URL-адресам. Для двух предыдущих файлов конфигурации они будут `https://localhost:44321/signin-oidc`. Причина в `applicationUrl` том, что есть, `http://localhost:3110`но `sslPort` указан (44321). `CallbackPath`является, `/signin-oidc`как `appsettings.json`определено в .

Таким же образом, вывески URI `https://localhost:44321/signout-callback-oidc`будет установлен на .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

В ASP.NET приложение настраивается через файл [Web.config,](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) строки от 12 до 15.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
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

На портале Azure, ответы URIs, что вам нужно зарегистрироваться на странице **аутентификации** для приложения должны соответствовать этим URL-адресам. То есть, они `https://localhost:44326/`должны быть .

# <a name="java"></a>[Java](#tab/java)

В Java конфигурация находится в файле [application.properties,](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) расположенном под `src/main/resources`.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

На портале Azure УРИ ответа, которые необходимо зарегистрировать на странице **аутентификации** для приложения, должны соответствовать `redirectUri` экземплярам, которые определяет приложение. То есть они `http://localhost:8080/msal4jsample/secure/aad` должны `http://localhost:8080/msal4jsample/graph/me`быть и .

# <a name="python"></a>[Python](#tab/python)

Вот файл конфигурации Python в [app_config.py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py):

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Этот quickstart предлагает хранить секрет клиента в файле конфигурации для простоты. В приложении для производства необходимо использовать другие способы хранения секрета, такие как хранилище ключей или переменная среды, описанная в [документации Flask.](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Код инициализации

Код инициализации отличается в зависимости от платформы. Для ASP.NET Core и ASP.NET, подписание в пользователях делегируется в программу OpenID Connect. Шаблон ASP.NET или ASP.NET Core генерирует веб-приложения для конечных точек Azure Active Directory (Azure AD) v1.0. Для их адаптации к идотовой платформе Майкрософт (v2.0) требуется некоторая конфигурация. В случае Java, он обрабатывается Весной с сотрудничеством с приложением.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

В ASP.NET основных веб-приложений (и веб-AI) приложение защищено, поскольку у вас есть `[Authorize]` атрибут на контроллерах или действиях контроллера. Этот атрибут проверяет подлинность пользователя. Код, инициализирующий приложение, находится в Startup.cs файле.

Чтобы добавить аутентификацию с платформой идентификации Майкрософт (ранее Azure AD v2.0), необходимо добавить следующий код. Комментарии в кодексе должны быть понятны.

> [!NOTE]
> Если вы начинаете свой проект с веб-проекта по `dotnet new mvc`умолчанию `AddAzureAD` ASP.NET Core в студии Visual или с помощью, метод доступен по умолчанию. Это связано с ними пакеты автоматически загружаются.
>
> Если вы создаете проект с нуля и пытаетесь использовать следующий код, мы предлагаем добавить пакет NuGet **Microsoft.AspNetCore.Authentication.AzureAD.UI** в свой проект, чтобы сделать `AddAzureAD` метод доступным.

Следующий код доступен на сайтах [Startup.cs-L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34).

```csharp
public class Startup
{
 ...

  // This method is called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);

      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

Метод `AddMicrosoftIdentityPlatformAuthentication` расширения определен в [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs-L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). Это:

- Добавляет службу аутентификации.
- Настраивает параметры для чтения файла конфигурации.
- Настраивает параметры OpenID Connect таким образом, чтобы используемый орган — это идентификационная платформа Майкрософт (ранее Azure AD v2.0) конечная точка.
- Проверяет эмитента токена.
- Гарантирует, что требования, соответствующие имени, `preferred_username` отображаются из претензии в маркере идентификатора.

В дополнение к конфигурации можно указать название раздела конфигурации при вызове. `AddMicrosoftIdentityPlatformAuthentication` По умолчанию, `AzureAd`это .

Отслеживание событий промежуточного программного обеспечения OpenId Connect может помочь вам устранить неполадки в веб-приложении, если аутентификация не работает. Установка `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` `true` покажет вам, как информация получает разработаннабор набором ASP.NET Core промежуточного по мере его `HttpContext.User`продвижения от http ответ на личность пользователя в .

```csharp
/// <summary>
/// Add authentication with the Microsoft identity platform.
/// This method expects the configuration file to have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenID Connect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the following code, this application signs in users in any work or school
      // accounts and any Microsoft personal accounts.
      // If you want to direct Azure AD to restrict the users who can sign in, change
      // the tenant value of the appsettings.json file in the following way:
      // - Only work or school accounts => 'organizations'
      // - Only Microsoft personal accounts => 'consumers'
      // - Work or school and personal accounts => 'common'
      // If you want to restrict the users who can sign in to only one tenant,
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization.
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users who can sign in to several organizations,
      // set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to the options.TokenValidationParameters.ValidIssuers collection.
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set nameClaimType to be preferred_username.
      // This change is needed because certain token claims from the Azure AD v1 endpoint
      // (on which the original .NET Core template is based) are different from the Microsoft identity platform endpoint.
      // For more details, see [ID tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

Класс `AadIssuerValidator` позволяет проверить эмитента токена во многих случаях. Этот класс работает с токеном v1.0 или v2.0, приложением с одним арендатором или мультитенантом или приложением, которое подписывает пользователей с их личными учетными записями Майкрософт в общедоступном облаке Azure или национальных облаках. Он доступен на [сайте Microsoft.Identity.Web/Resource/AadIssuerValidator.cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Код, связанный с аутентификацией в ASP.NET веб-приложении и веб-аПО, находится в файле [App_Start/Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61)

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

В образце Java используется платформа Spring. Приложение защищено, потому что вы реализуете фильтр, который перехватывает каждый ответ HTTP. В quickstart для Веб-приложений `AuthFilter` Java, этот фильтр находится в `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`.

Фильтр обрабатывает поток кода авторизации OAuth 2.0 и проверяет,`isAuthenticated()` проверен ли пользователь (метод). Если пользователь не проверен, он вычисляет URL конечных точек авторизации Azure AD и перенаправляет браузер в этот URI.

Когда ответ поступает, содержащий код авторизации, он приобретает токен с помощью MSAL Java. Когда он, наконец, получает токен из конечной точки токена (на перенаправлении URI), пользователь впишется в в водную точку.

Подробности см. `doFilter()` [AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)

> [!NOTE]
> Код `doFilter()` написан в несколько ином порядке, но поток описан.

Для получения подробной информации о потоке [Microsoft identity platform and OAuth 2.0 authorization code flow](v2-oauth2-auth-code-flow.md)кода авторизации, который вызывает этот метод, см.

# <a name="python"></a>[Python](#tab/python)

В образце Python используется колба. Инициализация колбы и MSAL Python выполнена в [app.py'L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Дальнейшие действия

В следующей статье вы узнаете, как инициировать регистрацию и входе.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Функции входа и выхода](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Функции входа и выхода](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Функции входа и выхода](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Функции входа и выхода](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
