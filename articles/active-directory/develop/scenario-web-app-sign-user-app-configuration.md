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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a746b0f6d85e3f012cdd2e78fff8cd10a586950
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086752"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Веб-приложение, которое входит в систему пользователей — конфигурация кода

Узнайте, как настроить код для веб-приложения, которое входит в систему пользователей.

## <a name="libraries-used-to-protect-web-apps"></a>Библиотеки, используемые для защиты веб-приложений

<!-- This section can be in an include for Web App and Web APIs -->
Библиотеки, используемые для защиты веб-приложения (и веб-API):

| Платформа | Библиотека | Описание |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Расширения модели удостоверений для .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Службы Microsoft Identity Extensions для .NET предлагают набор библиотек DLL, работающих как в .NET Framework, так и в .NET Core, непосредственно с помощью ASP.NET и ASP.NET Core. Из веб-приложения ASP.NET/ASP.NET Core можно управлять проверкой маркера с помощью класса **TokenValidationParameters** (в частности, в некоторых сценариях ISV). |
| ![Java](media/sample-v2-code/logo_java.png) | [msal4j](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | MSAL для Java — в настоящее время общедоступная Предварительная версия |
| ![Python](media/sample-v2-code/logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL для Python — сейчас в общедоступной предварительной версии |

Фрагменты кода в этой статье и следующие извлекаются из:

- [Пошаговое руководство по ASP.NET Core веб-приложения, глава 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).
- [Пример веб-приложения ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)
- Пример веб- [приложения Java, вызывающего](https://github.com/Azure-Samples/ms-identity-java-webapp) веб-приложение Microsoft Graph msal4j
- [веб-приложение Python, вызывающее Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) MSAL. Пример веб-приложения Python

Вы можете обратиться к этим руководствам и примерам для получения полной информации о реализации.

## <a name="configuration-files"></a>Файлы конфигурации

Веб-приложения, которые входят в систему пользователей с платформой Microsoft Identity, обычно настраиваются с помощью файлов конфигурации. Параметры, которые необходимо заполнить:

- облако `Instance` , если вы хотите, чтобы приложение выполнялось (например, в национальных облаках).
- аудитория в`tenantId`
- `clientId` для приложения, скопированное из портал Azure.

В `authority`некоторых случаях приложения могут быть параметризованным с помощью, которое является объединением `instance` и`tenantId`

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

В ASP.NET Core эти параметры находятся в `appsettings.json` файле в разделе "AzureAD".

```Json
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

В ASP.NET Core есть другой файл (`properties\launchSettings.json`), содержащий URL-адрес (`applicationUrl`) и порт SSL (`sslPort`) для приложения и различных профилей.

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

В портал Azure идентификаторы URI ответа, которые необходимо зарегистрировать на странице **проверки** подлинности для вашего приложения, должны соответствовать этим URL-адресам. Таким образом, для двух файлов конфигурации, приведенных выше, они `https://localhost:44321/signin-oidc` бы были как `http://localhost:3110` applicationUrl, но `sslPort` задано значение (44321) `appsettings.json`, а `CallbackPath` — `/signin-oidc` как определено в.
  
Таким же образом URI выхода будет иметь значение `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

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

В портал Azure идентификаторы URI ответа, которые необходимо зарегистрировать на странице **проверки подлинности** для вашего приложения, должны соответствовать этим URL-адресам. то есть `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

В Java конфигурация находится в файле, `application.properties` расположенном в папке`src/main/resources`

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraphUsers=http://localhost:8080/msal4jsample/graph/users
```

В портал Azure идентификаторы URI ответа, которые необходимо зарегистрировать на странице **проверки подлинности** для приложения, должны совпадать с редиректурис, определенным приложением, то есть `http://localhost:8080/msal4jsample/secure/aad` и`http://localhost:8080/msal4jsample/graph/users`

# <a name="pythontabpython"></a>[Python](#tab/python)

Ниже приведен файл конфигурации Python в файле [app_config. корректировки.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/web_app_sample/app_config.py)

```Python
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
SCOPE = ["https://graph.microsoft.com/User.Read"]
REDIRECT_URI = "http://localhost:5000/getAToken"
```

---

## <a name="initialization-code"></a>Код инициализации

Код инициализации различается в зависимости от платформы. Для ASP.NET Core и ASP.NET при входе в систему пользователи делегируются по промежуточного слоя OpenIDConnect. Сегодня шаблон ASP.NET/ASP.NET Core создает веб-приложения для конечной точки Azure AD версии 1.0. Таким образом, для адаптации их к конечной точке платформы Microsoft Identity Platform (v 2.0) требуется несколько конфигураций. В случае с Java оно обрабатывается пружиной с совместным использованием приложения.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

В ASP.NET Core веб-приложениях (и веб-API) приложение защищено, так как у `[Authorize]` вас есть атрибут на контроллерах или действия контроллера. Этот атрибут проверяет, прошел ли пользователь проверку подлинности. Код, выполняющий инициализацию приложения, находится в `Startup.cs` файле, и для добавления проверки подлинности с платформой Microsoft Identity (прежнее название — Azure AD 2.0) необходимо добавить следующий код. Комментарии в коде должны быть описательными.

  > [!NOTE]
  > Если вы запускаете проект с веб-проектом ASP.NET Core по умолчанию в Visual `dotnet new mvc` Studio или `AddAzureAD` с помощью метода, он доступен по умолчанию, так как связанные пакеты автоматически загружаются.
  > Однако если вы создаете проект с нуля и пытаетесь использовать приведенный ниже код, мы рекомендуем добавить пакет NuGet **Microsoft. AspNetCore. Authentication. AzureAD. UI** в проект, чтобы сделать этот `AddAzureAD` метод доступным.
  
Следующий код доступен в [запуске. CS # l33-l34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)

```CSharp
public class Startup
{
 ...

  // This method gets called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign-in users with the Microsoft identity platform
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

— Это метод расширения, определенный в [Microsoft. Identity. Web/вебаппсервицеколлектионекстенсионс. CS # L23.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23) `AddMicrosoftIdentityPlatformAuthentication` Им

- Добавляет службу проверки подлинности
- Настройка параметров для чтения файла конфигурации
- Настраивает параметры подключения OpenID Connect, чтобы используемая учетная запись была конечной точкой платформы Microsoft Identity (прежнее название — Azure AD 2.0).
- издатель маркера проверен
- утверждения, соответствующие имени, сопоставляются с утверждением "preferred_username" в маркере идентификации. 

В дополнение к конфигурации можно указать, при вызове `AddMicrosoftIdentityPlatformAuthentication`:

- имя раздела конфигурации (по умолчанию AzureAD)
- Если вы хотите отследить события по промежуточного слоя OpenIdConnect, которые могут помочь в устранении неполадок в веб-приложении `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` , `true` если проверка подлинности не работает: параметр для показывает, как будет изменяться информация по набору ASP.NET Core по промежуточного слоя по мере продвижения от HTTP-ответа к удостоверению пользователя в `HttpContext.User`.

```CSharp
/// <summary>
/// Add authentication with Microsoft identity platform.
/// This method expects the configuration file will have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenIdConnect events.
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
      // Per the code below, this application signs in users in any Work and School
      // accounts and any Microsoft Personal Accounts.
      // If you want to direct Azure AD to restrict the users that can sign-in, change
      // the tenant value of the appsettings.json file in the following way:
      // - only Work and School accounts => 'organizations'
      // - only Microsoft Personal accounts => 'consumers'
      // - Work and School and Personal accounts => 'common'
      // If you want to restrict the users that can sign-in to only one tenant
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users that can sign-in to several organizations
      // Set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set the nameClaimType to be preferred_username.
      // This change is needed because certain token claims from Azure AD V1 endpoint
      // (on which the original .NET core template is based) are different than Microsoft identity platform endpoint.
      // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
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

`AadIssuerValidator` Класс обеспечивает проверку издателя маркера во многих случаях (маркер v 1.0 или v 2.0 Token, одноклиентское или многоклиентское приложение или приложение, которое входит в систему пользователей с помощью личных учетных записей Майкрософт, в общедоступном облаке Azure или национальной облака). Он доступен в [Microsoft. Identity. Web/Resource/аадиссуервалидатор. CS](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Код, связанный с проверкой подлинности в ASP.NET веб-приложениях и веб `App_Start/Startup.Auth.cs` -интерфейсах API, находится в файле.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0
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

# <a name="javatabjava"></a>[Java](#tab/java)

В примере Java используется пружинная платформа. Приложение защищено `Filter`, так как реализуется, который получает каждый HTTP-ответ. В кратком руководстве по веб-приложению Java `AuthFilter` это `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`в. Фильтр обрабатывает поток кода авторизации OAuth 2,0 и поэтому:

- проверяет, прошел ли пользователь проверку подлинности`isAuthenticated()` (метод)
- Если пользователь не прошел проверку подлинности, он вычислит URL-адреса авторизации Azure AD и перенаправит браузер на этот URI.
- Когда получен ответ, содержащий поток кода проверки подлинности, он позволяет msal4j получение маркера.
- когда он наконец получает маркер из конечной точки маркера (в URI перенаправления), пользователь вошел в систему.

Дополнительные сведения см. `doFilter()` в описании метода в [аусфилтер. Java.](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)

> [!NOTE]
> Код `doFilter()` написан немного иначе, но последовательность описана в этом порядке.

Дополнительные сведения о потоке кода авторизации, инициированном этим методом, см. в [статье поток кода авторизации Microsoft Identity и OAuth 2,0](v2-oauth2-auth-code-flow.md) .

# <a name="pythontabpython"></a>[Python](#tab/python)

В образце Python используется Flask. Инициализация Flask и MSAL. Python выполняется в [app. Корректировка # L1-L17](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/app.py#L1-L17)

```Python
import uuid
import flask
import requests
from flask import Flask, render_template, session, request
from flask_session import Session
import msal
import app_config

sess = Session()
app = Flask(__name__)
app.config.from_object('config.Config')
sess.init_app(app)
cache = msal.SerializableTokenCache()
application = msal.ConfidentialClientApplication(
    app_config.CLIENT_ID, authority=app_config.AUTHORITY,
    client_credential=app_config.CLIENT_SECRET,
    token_cache=cache)
```

Это MSAL. Python, который позаботится о том, чтобы позволить пользователю войти в систему. См [. раздел App. Корректировка # L74-84](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/app.py#L74-84)

```Python
@app.route('/authenticate')
def authenticate():
    # Call to the authorize endpoint
    auth_state = str(uuid.uuid4())
    session[(request.cookies.get("session")+'state')] = auth_state
    authorization_url = application.get_authorization_request_url(app_config.SCOPE, state=auth_state,
                                                                  redirect_uri=app_config.REDIRECT_URI)
    resp = flask.Response(status=307)
    resp.headers['location'] = authorization_url
    return resp
```

---

## <a name="next-steps"></a>Следующие шаги

В следующей статье вы узнаете, как активировать вход и выход.

> [!div class="nextstepaction"]
> [Вход и выход](scenario-web-app-sign-user-sign-in.md)
