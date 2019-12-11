---
title: Настройка веб-приложения, которое входит в систему пользователей — платформа Microsoft Identity | Службы
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b077a71a541d29c9b93778babc096ea40c3b43cb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964877"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Веб-приложение, которое входит в систему пользователей: конфигурация кода

Узнайте, как настроить код для веб-приложения, которое входит в систему пользователей.

## <a name="libraries-for-protecting-web-apps"></a>Библиотеки для защиты веб-приложений

<!-- This section can be in an include for Web App and Web APIs -->
Библиотеки, используемые для защиты веб-приложения (и веб-API):

| платформа | Библиотека | Описание |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Расширения модели удостоверений для .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | По ASP.NET и ASP.NET Core, расширения модели идентификации Майкрософт для .NET предлагают набор библиотек DLL, выполняющихся как в .NET Framework, так и в .NET Core. Из веб-приложения ASP.NET или ASP.NET Core можно управлять проверкой маркера с помощью класса **TokenValidationParameters** (в частности, в некоторых сценариях партнеров). |
| ![Java:](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Библиотека проверки подлинности Майкрософт (MSAL) для Java. В настоящее время общедоступная Предварительная версия. |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL для Python. В настоящее время общедоступная Предварительная версия. |

Выберите вкладку, соответствующую интересующей вас платформе:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Фрагменты кода в этой статье и следующие извлекаются из [пошагового руководства по ASP.NET Core веб-приложению, главе 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Для получения подробных сведений о реализации может потребоваться обратиться к этому учебнику.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Фрагменты кода в этой статье и следующие извлекаются из [примера веб-приложения ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

Для получения полной информации о реализации можно использовать этот пример.

# <a name="javatabjava"></a>[Java](#tab/java)

Фрагменты кода в этой статье и следующие извлекаются из примера [веб-приложения Java, вызывающего Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) в MSAL Java.

Для получения полной информации о реализации можно использовать этот пример.

# <a name="pythontabpython"></a>[Python](#tab/python)

Фрагменты кода в этой статье и следующие извлекаются из примера [веб-приложения Python, вызывающего Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) в MSAL Python.

Для получения полной информации о реализации можно использовать этот пример.

---

## <a name="configuration-files"></a>Файлы конфигурации

Веб-приложения, которые входят в систему пользователей с помощью платформы Microsoft Identity, обычно настраиваются с помощью файлов конфигурации. Параметры, которые необходимо заполнить:

- Облачный экземпляр (`Instance`), если вы хотите, чтобы приложение выполнялось в национальных облаках, например
- Аудитория в ИДЕНТИФИКАТОРе клиента (`TenantId`)
- Идентификатор клиента (`ClientId`) для приложения, скопированный из портал Azure

В некоторых случаях приложения могут быть параметризованным с помощью `Authority`, который представляет собой объединение `Instance` и `TenantId`.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

В ASP.NET Core эти параметры находятся в файле [appSettings. JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) в разделе "AzureAd".

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
    "TenantId": "[Enter the tenantId here]]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

В ASP.NET Core другой файл ([properties\launchSettings.JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) содержит URL-адрес (`applicationUrl`) и порт SSL (`sslPort`) для приложения и различных профилей.

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

В портал Azure идентификаторы URI ответа, которые необходимо зарегистрировать на странице **проверки подлинности** для вашего приложения, должны соответствовать этим URL-адресам. Для двух предыдущих файлов конфигурации они будут `https://localhost:44321/signin-oidc`. Причина в том, что `applicationUrl` `http://localhost:3110`, но `sslPort` указана (44321). `CallbackPath` `/signin-oidc`, как определено в `appsettings.json`.

Таким же образом универсальный код ресурса (URI) выхода будет иметь значение `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

В ASP.NET приложение настраивается с помощью файла [Web. config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) , строки 12 – 15.

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

В портал Azure идентификаторы URI ответа, которые необходимо зарегистрировать на странице **проверки подлинности** для вашего приложения, должны соответствовать этим URL-адресам. То есть они должны быть `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

В Java конфигурация находится в файле [приложения. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) , расположенном в разделе `src/main/resources`.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

В портал Azure URI-коды ответа, которые необходимо зарегистрировать на странице **проверки подлинности** для приложения, должны соответствовать экземплярам `redirectUri`, определяемым приложением. То есть они должны быть `http://localhost:8080/msal4jsample/secure/aad` и `http://localhost:8080/msal4jsample/graph/me`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Ниже приведен файл конфигурации Python в [app_config. Корректировка](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py):

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> В этом кратком руководстве предлагается сохранить секрет клиента в файле конфигурации для простоты. В рабочем приложении необходимо использовать другие способы хранения секрета, например хранилище ключей или переменную среды, как описано в [документации по Flask](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables).
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Код инициализации

Код инициализации различается в зависимости от платформы. Для ASP.NET Core и ASP.NET при входе в систему пользователи делегируются по промежуточного слоя OpenID Connect Connect. Шаблон ASP.NET или ASP.NET Core создает веб-приложения для конечной точки Azure Active Directory (Azure AD) v 1.0. Для адаптации их к конечной точке платформы Microsoft Identity Platform (v 2.0) требуется определенная конфигурация. В случае с Java оно обрабатывается пружиной с совместным использованием приложения.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

В ASP.NET Core веб-приложениях (и веб-API) приложение защищено, так как на контроллерах или в действиях контроллера имеется атрибут `[Authorize]`. Этот атрибут проверяет, прошел ли пользователь проверку подлинности. Код, который инициализирует приложение, находится в файле Startup.cs. 

Чтобы добавить проверку подлинности на платформе Microsoft Identity (прежнее название — Azure AD 2.0), необходимо добавить следующий код. Комментарии в коде должны быть описательными.

> [!NOTE]
> Если вы запускаете проект с веб-проектом ASP.NET Core по умолчанию в Visual Studio или с помощью `dotnet new mvc`, метод `AddAzureAD` доступен по умолчанию. Это связано с тем, что связанные пакеты автоматически загружаются.
>
> Если вы создаете проект с нуля и пытаетесь использовать приведенный ниже код, мы рекомендуем добавить в проект пакет NuGet **Microsoft. AspNetCore. Authentication. AzureAD. UI** , чтобы сделать доступным метод `AddAzureAD`.

Следующий код доступен в [запуске. CS # l33-l34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34).

```CSharp
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

Метод расширения `AddMicrosoftIdentityPlatformAuthentication` определен в [Microsoft. Identity. Web/вебаппсервицеколлектионекстенсионс. CS # L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). Им

- Добавляет службу проверки подлинности.
- Настраивает параметры для чтения файла конфигурации.
- Настраивает параметры OpenID Connect Connect, чтобы используемая учетная запись была конечной точкой платформы Microsoft Identity (прежнее название — Azure AD 2.0).
- Проверяет издателя маркера.
- Гарантирует, что утверждения, соответствующие имени, сопоставляются с утверждением `preferred_username` в маркере идентификации.

В дополнение к конфигурации можно указать имя раздела конфигурации при вызове `AddMicrosoftIdentityPlatformAuthentication`. По умолчанию это `AzureAd`.

Трассировка событий по промежуточного слоя OpenID Connect позволяет устранить неполадки в веб-приложении, если проверка подлинности не работает. При установке `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` в `true` будет показано, каким образом данные изменяются набором по промежуточного слоя ASP.NET Core по мере продвижения от HTTP-ответа к удостоверению пользователя в `HttpContext.User`.

```CSharp
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

Класс `AadIssuerValidator` позволяет проверять издатель маркера во многих случаях. Этот класс работает с маркером v 1.0 или v 2.0, одним или несколькими клиентами или приложением, которое выполняет вход пользователей с помощью личных учетных записей Майкрософт в общедоступном облаке Azure или в национальных облаках. Он доступен в [Microsoft. Identity. Web/Resource/аадиссуервалидатор. CS](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Код, связанный с проверкой подлинности в веб-приложении ASP.NET и веб-API, находится в файле [App_Start/стартуп.АУС.КС](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) .

```CSharp
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

# <a name="javatabjava"></a>[Java](#tab/java)

В примере Java используется пружинная платформа. Приложение защищено, так как вы реализуете фильтр, который перехватывает каждый HTTP-ответ. В кратком руководстве по веб-приложениям Java этот фильтр `AuthFilter` в `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`. 

Фильтр обрабатывает поток кода авторизации OAuth 2,0 и проверяет, прошел ли пользователь проверку подлинности (`isAuthenticated()` метод). Если пользователь не прошел проверку подлинности, он рассчитывает URL-адрес конечных точек авторизации Azure AD и перенаправляет браузер на этот универсальный код ресурса (URI).

Когда получен ответ, содержащий код авторизации, он получает маркер с помощью MSAL Java. Когда он наконец получает маркер из конечной точки маркера (в URI перенаправления), пользователь вошел в систему.

Дополнительные сведения см. в описании метода `doFilter()` в [аусфилтер. Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> Код `doFilter()` записывается немного иначе, но последовательность описана в этом порядке.

Дополнительные сведения о потоке кода авторизации, который запускается этим методом, см. в [статье поток кода авторизации платформы Microsoft Identity и OAuth 2,0](v2-oauth2-auth-code-flow.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

В образце Python используется Flask. Инициализация Flask и MSAL Python выполняется в [app. копировать # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

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

В следующей статье вы узнаете, как активировать вход и выход.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Вход и выход](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Вход и выход](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Вход и выход](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Вход и выход](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
