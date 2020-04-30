---
title: Настройка веб-приложения, вызывающего веб-API — платформа Microsoft Identity | Службы
description: Узнайте, как настроить код веб-приложения, вызывающего веб-API
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
ms.openlocfilehash: 68f6f8ec67aca44c89b338287bdd37b6066992e0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207026"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Веб-приложение, вызывающее веб-API: конфигурация кода

Как показано в примере с [веб-приложением, в котором выполняется вход пользователей](scenario-web-app-sign-user-overview.md) , веб-приложение использует [поток кода авторизации OAuth 2,0](v2-oauth2-auth-code-flow.md) для входа пользователя. Этот поток включает два шага:

1. Запросите код авторизации. Эта часть делегирует частное диалоговое окно с пользователем на платформу Microsoft Identity. Во время этого диалогового окна пользователь входит в систему и отправляется на использование веб-API. Когда диалоговое окно закрыто успешно завершается, веб-приложение получает код авторизации в URI перенаправления.
1. Запросите маркер доступа для API с помощью активации кода авторизации.

[Веб-приложение, которое подписывает сценарии пользователей,](scenario-web-app-sign-user-overview.md) охватывает только первый шаг. Здесь вы узнаете, как изменить веб-приложение так, чтобы оно не только подписывает пользователей, но и теперь вызывало веб-API.

## <a name="libraries-that-support-web-app-scenarios"></a>Библиотеки, поддерживающие сценарии веб-приложений

Следующие библиотеки в библиотеке проверки подлинности Майкрософт (MSAL) поддерживают поток кода авторизации для веб-приложений:

| Библиотека MSAL | Описание |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Поддержка платформ .NET Framework и .NET Core. Не поддерживается универсальная платформа Windows (UWP), Xamarin. iOS и Xamarin. Android, так как эти платформы используются для создания общедоступных клиентских приложений. Для ASP.NET Core веб-приложений и веб-интерфейсов API MSAL.NET инкапсулируется в библиотеку более высокого уровня с именем Microsoft. Identity. Web.|
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL для Python | Поддержка веб-приложений Python. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL для Java | Поддержка веб-приложений Java. |

Выберите вкладку для интересующей вас платформы:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Чтобы веб-приложение вызывало защищенные интерфейсы API при использовании Microsoft. Identity. Web, необходимо только вызвать `AddWebAppCallsProtectedWebApi` и указать формат сериализации кэша маркеров (например, кэш маркеров в памяти):

```C#
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    // more code here

    services.AddSignIn(Configuration, "AzureAd")
            .AddWebAppCallsProtectedWebApi(Configuration,
                                           initialScopes: new string[] { "user.read" })
            .AddInMemoryTokenCaches();

    // more code here
}
```

Если вы хотите получить дополнительные сведения о кэше маркеров, см. раздел [Параметры сериализации кэша маркеров](#token-cache) .

> [!NOTE]
> Чтобы полностью понять примеры кода, необходимо ознакомиться с [ASP.NET Core фундаментальными принципами](https://docs.microsoft.com/aspnet/core/fundamentals)и в частности с [внедрением](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) и [параметрами](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)зависимостей.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Поскольку вход пользователя делегируется по промежуточного слоя Open ID Connect (OIDC), необходимо взаимодействовать с процессом OIDC. Взаимодействие зависит от используемой платформы.

Для ASP.NET вы подпишитесь на события OIDC по промежуточного слоя:

- Вы можете разрешить ASP.NET Core запросить код авторизации с помощью по промежуточного слоя Open ID Connect. ASP.NET или ASP.NET Core предоставит пользователю возможность входа и предоставления согласия.
- Вы подпишитесь на веб-приложение, чтобы получить код авторизации. Эта подписка выполняется с помощью делегата C#.
- При получении кода авторизации вы будете использовать библиотеки MSAL для его активации. Итоговые маркеры доступа и маркеры обновления хранятся в кэше маркеров. Кэш можно использовать в других частях приложения, таких как контроллеры, для получения других маркеров без вмешательства пользователя.

Примеры кода, приведенные в этой статье, и следующие из них извлекаются из [примера веб-приложения ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Для получения подробных сведений о реализации может потребоваться обратиться к этому примеру.

# <a name="java"></a>[Java](#tab/java)

Примеры кода в этой статье и следующие сведения извлекаются из [веб-приложения Java, которое вызывает Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), пример веб-приложения, в котором используется MSAL для Java.
В настоящее время пример позволяет MSAL для Java создать URL-адрес авторизации и обработать переход к конечной точке авторизации для платформы Microsoft Identity. Можно также использовать безопасность спринта для входа пользователя в систему. Для получения подробных сведений о реализации может потребоваться ссылка на пример.

# <a name="python"></a>[Python](#tab/python)

Примеры кода, приведенные в этой статье, и следующие из них извлекаются из веб-приложения Python, в котором [вызывается Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), пример Web App, использующий MSAL. Языке.
В настоящее время пример позволяет MSAL. Python создает URL-адрес кода авторизации и обрабатывает переход к конечной точке авторизации для платформы Microsoft Identity. Для получения подробных сведений о реализации может потребоваться ссылка на пример.

---

## <a name="code-that-redeems-the-authorization-code"></a>Код, который активирует код авторизации

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity. Web упрощает ваш код, настроив правильные параметры OpenID Connect Connect, подписавшись на событие Received Code и выключая код. Для активации кода авторизации дополнительный код не требуется.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET обрабатывает вещи так же, как ASP.NET Core, за исключением того, что конфигурация OpenID Connect Connect и `OnAuthorizationCodeReceived` подписка на событие происходит в файле [\стартуп.АУС.КС App_Start](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) . Эти понятия также похожи на ASP.NET Core, за исключением того, что в ASP.NET необходимо указать `RedirectUri` в [файле Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Такая конфигурация немного менее надежна, чем в ASP.NET Core, так как ее необходимо изменить при развертывании приложения.

Вот код для Startup.Auth.cs:

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

См. раздел [веб-приложение, которое входит в систему пользователей: конфигурация кода](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) , чтобы понять, как пример кода Java получает код авторизации. После того как приложение получит код, [аусфилтер. Java # L51-l56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Делегаты для `AuthHelper.processAuthenticationCodeRedirect` метода в [AuthHelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Вызывает `getAuthResultByAuthCode`.

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

`getAuthResultByAuthCode` Метод определен в [AuthHelper. Java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Он создает MSAL `ConfidentialClientApplication`, а затем вызывает метод `acquireToken()` , `AuthorizationCodeParameters` созданный из кода авторизации.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

Будет запрошен поток кода авторизации, как показано в [веб-приложении, которое выполняет вход в систему пользователей: конфигурация кода](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). Затем код получается от `authorized` функции, Flask маршруты от `/getAToken` URL-адреса. Полный контекст этого кода см. в разделе [app. Корректировка # L30-l44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) .

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Вместо секрета клиента конфиденциальное клиентское приложение также может доказать свою личность с помощью сертификата клиента или утверждения клиента.
Использование утверждений клиента является расширенным сценарием, подробно описанным в [проверочных утверждениях клиента](msal-net-client-assertions.md).

## <a name="token-cache"></a>Кэш маркеров

> [!IMPORTANT]
> Реализация кэша токенов для веб-приложений или веб-API отличается от реализации для классических приложений, которые часто [основаны на файлах](scenario-desktop-acquire-token.md#file-based-token-cache).
> По соображениям безопасности и производительности важно убедиться, что для веб-приложений и веб-API существует один кэш маркеров на одну учетную запись пользователя. Необходимо сериализовать кэш маркеров для каждой учетной записи.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

В руководстве по ASP.NET Core используется внедрение зависимостей, позволяющее выбрать реализацию кэша маркеров в файле Startup.cs для приложения. Microsoft. Identity. Web поставляется с предварительно созданными сериализаторами кэша маркеров, описанными в разделе [сериализация кэша маркеров](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Интересной возможностью является выбор ASP.NET Core [распределенных кэшей памяти](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache):

```csharp
// Use a distributed token cache by adding:
    services.AddSignIn(Configuration, "AzureAd");
            .AddWebAppCallsProtectedWebApi(Configuration,
                                           initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache()

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Дополнительные сведения о поставщиках кэша маркеров см. также в [учебниках по веб-приложениям ASP.NET Core | Стадия работы с кэшами маркеров](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) учебника.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Реализация кэша токенов для веб-приложений или веб-API отличается от реализации для классических приложений, которые часто [основаны на файлах](scenario-desktop-acquire-token.md#file-based-token-cache).

Реализация веб-приложения может использовать сеанс ASP.NET или серверную память. Например, Узнайте, как реализация кэша подключается после создания приложения MSAL.NET в [мсалаппбуилдер. CS # l39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

MSAL Java предоставляет методы для сериализации и десериализации кэша маркеров. Пример Java обрабатывает сериализацию из сеанса, как показано в `getAuthResultBySilentFlow` методе в [AuthHelper. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Подробные сведения о `SessionManagementHelper` классе приведены в [примере MSAL для Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="python"></a>[Python](#tab/python)

В примере Python один кэш на учетную запись обеспечивается путем повторного создания конфиденциального клиентского приложения для каждого запроса и последующей его сериализации в кэш сеанса Flask:

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>Дальнейшие шаги

На этом этапе при входе пользователя маркер сохраняется в кэше маркеров. Давайте посмотрим, как он используется в других частях веб-приложения.

> [!div class="nextstepaction"]
> [Веб-приложение, вызывающее веб-API: удаление учетных записей из кэша при глобальном выходе](scenario-web-app-call-api-sign-in.md)
