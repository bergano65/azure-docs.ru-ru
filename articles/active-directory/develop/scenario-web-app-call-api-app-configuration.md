---
title: Настройка веб-приложения, вызывающего веб-API — платформа Microsoft Identity | Службы
description: Узнайте, как настроить код веб-приложения, вызывающего веб-API
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
ms.openlocfilehash: 374b215a737efbe3d421b6dc49af01303ec54473
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759166"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Веб-приложение, вызывающее веб-API: конфигурация кода

Как показано в примере с [веб-приложением, в котором выполняется вход пользователей](scenario-web-app-sign-user-overview.md) , веб-приложение использует [поток кода авторизации OAuth 2,0](v2-oauth2-auth-code-flow.md) для входа пользователя. Этот поток включает два шага:

1. Запросите код авторизации. Эта часть делегирует частное диалоговое окно с пользователем на платформу Microsoft Identity. Во время этого диалогового окна пользователь входит в систему и отправляется на использование веб-API. Когда диалоговое окно закрыто успешно завершается, веб-приложение получает код авторизации в URI перенаправления.
1. Запросите маркер доступа для API с помощью активации кода авторизации.

[Веб-приложение, которое подписывает сценарии пользователей,](scenario-web-app-sign-user-overview.md) охватывает только первый шаг. Здесь вы узнаете, как изменить веб-приложение так, чтобы оно не только подписывает пользователей, но и теперь вызывало веб-API.

## <a name="libraries-that-support-web-app-scenarios"></a>Библиотеки, поддерживающие сценарии веб-приложений

Следующие библиотеки в библиотеке проверки подлинности Майкрософт (MSAL) поддерживают поток кода авторизации для веб-приложений:

| Библиотека MSAL | Description |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Поддержка платформ .NET Framework и .NET Core. Не поддерживается универсальная платформа Windows (UWP), Xamarin. iOS и Xamarin. Android, так как эти платформы используются для создания общедоступных клиентских приложений. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL для Python | Поддержка веб-приложений Python. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL для Java | Поддержка веб-приложений Java. |

Выберите вкладку для интересующей вас платформы:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Поскольку вход пользователя делегируется по промежуточного слоя Open ID Connect (OIDC), необходимо взаимодействовать с процессом OIDC. Взаимодействие зависит от используемой платформы.

Для ASP.NET Core вы подпишитесь на события OIDC по промежуточного слоя:

- Вы можете разрешить ASP.NET Core запросить код авторизации с помощью по промежуточного слоя Open ID Connect. ASP.NET или ASP.NET Core предоставит пользователю возможность входа и предоставления согласия.
- Вы подпишитесь на веб-приложение, чтобы получить код авторизации. Эта подписка выполняется с помощью C# делегата.
- При получении кода авторизации вы будете использовать библиотеки MSAL для его активации. Итоговые маркеры доступа и маркеры обновления хранятся в кэше маркеров. Кэш можно использовать в других частях приложения, таких как контроллеры, для получения других маркеров без вмешательства пользователя.

Примеры кода в этой статье и приведенные далее извлекаются из [пошагового руководства по веб-приложению ASP.NET Core, глава 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Для получения подробных сведений о реализации может потребоваться обратиться к этому учебнику.

> [!NOTE]
> Чтобы полностью понять примеры кода, необходимо ознакомиться с [ASP.NET Core фундаментальными принципами](https://docs.microsoft.com/aspnet/core/fundamentals)и в частности с [внедрением](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) и [параметрами](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)зависимостей.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Поскольку вход пользователя делегируется по промежуточного слоя Open ID Connect (OIDC), необходимо взаимодействовать с процессом OIDC. Взаимодействие зависит от используемой платформы.

Для ASP.NET вы подпишитесь на события OIDC по промежуточного слоя:

- Вы можете разрешить ASP.NET Core запросить код авторизации с помощью по промежуточного слоя Open ID Connect. ASP.NET или ASP.NET Core предоставит пользователю возможность входа и предоставления согласия.
- Вы подпишитесь на веб-приложение, чтобы получить код авторизации. Эта подписка выполняется с помощью C# делегата.
- При получении кода авторизации вы будете использовать библиотеки MSAL для его активации. Итоговые маркеры доступа и маркеры обновления хранятся в кэше маркеров. Кэш можно использовать в других частях приложения, таких как контроллеры, для получения других маркеров без вмешательства пользователя.

Примеры кода, приведенные в этой статье, и следующие из них извлекаются из [примера веб-приложения ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Для получения подробных сведений о реализации может потребоваться обратиться к этому примеру.

# <a name="javatabjava"></a>[Java](#tab/java)

Примеры кода в этой статье и следующие сведения извлекаются из [веб-приложения Java, которое вызывает Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), пример веб-приложения, в котором используется MSAL для Java.
В настоящее время пример позволяет MSAL для Java создать URL-адрес авторизации и обработать переход к конечной точке авторизации для платформы Microsoft Identity. Можно также использовать безопасность спринта для входа пользователя в систему. Для получения подробных сведений о реализации может потребоваться ссылка на пример.

# <a name="pythontabpython"></a>[Python](#tab/python)

Примеры кода, приведенные в этой статье, и следующие из них извлекаются из веб-приложения Python, в котором [вызывается Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), пример Web App, использующий MSAL. Языке.
В настоящее время пример позволяет MSAL. Python создает URL-адрес кода авторизации и обрабатывает переход к конечной точке авторизации для платформы Microsoft Identity. Для получения подробных сведений о реализации может потребоваться ссылка на пример.

---

## <a name="code-that-redeems-the-authorization-code"></a>Код, который активирует код авторизации

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

В ASP.NET Core в файле `Startup.cs` вы подписываетесь на событие `OnAuthorizationCodeReceived` OpenID Connect Connect. В этом событии вызовите метод MSAL.NET `AcquireTokenFromAuthorizationCode`. Этот метод сохраняет в кэше маркеров следующие токены:

- *Маркер доступа* для запрошенного `scopes`.
- *Маркер обновления*. Этот маркер будет использоваться для обновления маркера доступа, если он близок к истечению срока действия или для получения другого маркера от имени того же пользователя, но для другого ресурса.

В [учебнике ASP.NET Core веб-приложений](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) вы также посвящены повторному использованию кода для веб-приложений.

Ниже приведен код из [Startup. CS # L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42). Он включает в себя вызовы:

- Метод `AddMicrosoftIdentityPlatformAuthentication`, который добавляет проверку подлинности в веб-приложение.
- Метод `AddMsal`, который добавляет возможность вызова веб-API.
- Метод `AddInMemoryTokenCaches`, который связан с выбором реализации кэша маркеров.

```csharp
public class Startup
{
  // Code not shown here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token-cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not shown here
}
```

`Constants.ScopeUserRead` определяется в [константах. CS # 5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5):

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Вы уже изсмотрели содержимое `AddMicrosoftIdentityPlatformAuthentication` в [веб-приложении, которое входит в систему пользователей — конфигурация кода](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code).

### <a name="the-addmsal-method"></a>Метод Аддмсал

Код для `AddMsal` находится в [Microsoft. Identity. Web/вебаппсервицеколлектионекстенсионс. CS # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the web app or web API.
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection.
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign in by using their personal Microsoft accounts.
          // (It's required by MSAL.NET and automatically provided when users sign in by using work or school accounts.)
          options.Scope.Add("offline_access");
          if (initialScopes != null)
          {
              foreach (string scope in initialScopes)
              {
                  if (!options.Scope.Contains(scope))
                  {
                      options.Scope.Add(scope);
                  }
              }
          }

          // Handle the auth redemption by MSAL.NET so that a token is available in the token cache,
          // where it will be usable from controllers later (by means of the TokenAcquisition service).
          var handler = options.Events.OnAuthorizationCodeReceived;
          options.Events.OnAuthorizationCodeReceived = async context =>
          {
              var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
              await tokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync(context, options.Scope).ConfigureAwait(false);
              await handler(context).ConfigureAwait(false);
          };
      });
      return services;
  }
}
```

Метод `AddMsal` гарантирует следующее:

- Веб-приложение ASP.NET Core запрашивает маркер идентификации для пользователя и код проверки подлинности (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`).
- Добавляется область `offline_access`. Эта область получает согласие пользователя на получение маркера обновления приложением.
- Приложение подписывается на событие OIDC `OnAuthorizationCodeReceived` и активирует вызов с помощью MSAL.NET, который инкапсулируется здесь в многократно используемый компонент, реализующий `ITokenAcquisition`.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>Метод Токенаккуиситион. Аддаккаунттокачефромаусоризатионкодеасинк

Метод `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` находится в [Microsoft. Identity. Web/токенаккуиситион. CS # L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Это гарантирует, что:

- ASP.NET не пытается активировать код проверки подлинности параллельно с MSAL.NET (`context.HandleCodeRedemption();`).
- Утверждения в маркере идентификации доступны для MSAL, чтобы вычислить ключ кэша маркера для учетной записи пользователя.
- При необходимости создается экземпляр приложения MSAL.NET.
- Код активируется приложением MSAL.NET.
- Новый маркер идентификатора используется совместно с ASP.NET Core во время вызова `context.HandleCodeRedemption(null, result.IdToken);`. Маркер доступа не используется совместно с ASP.NET Core. Он остается в кэше маркеров MSAL.NET, связанном с пользователем, где он готов к использованию в контроллерах ASP.NET Core.

Ниже приведен соответствующий код для `TokenAcquisition`.

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };

  // Code omitted here for clarity


  public async Task AddAccountToCacheFromAuthorizationCodeAsync(AuthorizationCodeReceivedContext context, IEnumerable<string> scopes)
  {
   // Code omitted here for clarity

    try
    {
      // Because AcquireTokenByAuthorizationCodeAsync is asynchronous, we tell ASP.NET core that we're handing the code
      // even if it's not done yet, so that it doesn't concurrently call the token endpoint. Otherwise, there will be a
      // race condition that causes an Azure AD error message ("code already redeemed").
      context.HandleCodeRedemption();

      // The cache needs the claims from the ID token.
      // If they're not yet in the HttpContext.User's claims, add them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Don't share the access token with ASP.NET Core. If we share it, ASP.NET will cache it and won't send the OAuth 2.0 request if
      // a further call to AcquireTokenByAuthorizationCodeAsync is required later for incremental consent (getting a code requesting more scopes).
      // Do share the ID token, however.
      var result = await application
          .AcquireTokenByAuthorizationCode(scopes.Except(_scopesRequestedByMsalNet), context.ProtocolMessage.Code)
          .ExecuteAsync()
          .ConfigureAwait(false);

      context.HandleCodeRedemption(null, result.IdToken);
  }
  catch (MsalException ex)
  {
      Debug.WriteLine(ex.Message);
      throw;
  }
 }
```

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>Метод Токенаккуиситион. Буилдконфидентиалклиентаппликатион

В ASP.NET Core сборка конфиденциального клиентского приложения использует сведения, содержащиеся в `HttpContext`. Доступ к `HttpContext`, связанному с запросом, осуществляется с помощью свойства `CurrentHttpContext`. `HttpContext` содержит сведения об URL-адресе для веб – приложения и о пользователе, выполнившего вход (в `ClaimsPrincipal`). 

Метод `BuildConfidentialClientApplication` также использует конфигурацию ASP.NET Core. Конфигурация содержит раздел "AzureAD", который также привязан к обоим следующим элементам:

- `_applicationOptions`ная структура данных типа [конфидентиалклиентаппликатионоптионс](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet).
- Экземпляр `azureAdOptions` типа [азуреадоптионс](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs), определенный в ASP.NET Core `Authentication.AzureAD.UI`.

Наконец, приложению необходимо поддерживать кэши маркеров. Дополнительные сведения об этом см. в следующем разделе.

Код для метода `GetOrBuildConfidentialClientApplication()` находится в [Microsoft. Identity. Web/токенаккуиситион. CS # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). В нем используются члены, которые были добавлены путем внедрения зависимостей (передаются в конструктор `TokenAcquisition` в [Microsoft. Identity. Web/токенаккуиситион. CS # L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)).

Ниже приведен код для `GetOrBuildConfidentialClientApplication`:

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor.
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL confidential client application, if needed.
  /// </summary>
  private IConfidentialClientApplication GetOrBuildConfidentialClientApplication()
  {
    if (application == null)
    {
        application = BuildConfidentialClientApplication();
    }
    return application;
  }

  /// <summary>
  /// Creates an MSAL Confidential client application
  /// </summary>
  /// <param name="claimsPrincipal"></param>
  /// <returns></returns>
  private IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    var request = CurrentHttpContext.Request;
    var azureAdOptions = _azureAdOptions;
    var applicationOptions = _applicationOptions;
    string currentUri = UriHelper.BuildAbsolute(
        request.Scheme,
        request.Host,
        request.PathBase,
        azureAdOptions.CallbackPath ?? string.Empty);

    string authority = $"{applicationOptions.Instance}{applicationOptions.TenantId}/";

    var app = ConfidentialClientApplicationBuilder
        .CreateWithApplicationOptions(applicationOptions)
        .WithRedirectUri(currentUri)
        .WithAuthority(authority)
        .Build();

    // Initialize token cache providers
    _appTokenCacheProvider?.InitializeAsync(app.AppTokenCache);
    _userTokenCacheProvider?.InitializeAsync(app.UserTokenCache);

    return app;
  }

```

### <a name="summary"></a>Сводка

в действительности `AcquireTokenByAuthorizationCode` — это метод, который активирует код авторизации, ASP.NET запросы, и который получает маркеры, которые добавляются в кэш пользовательских маркеров MSAL.NET. Из кэша маркеры затем используются на контроллерах ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET обрабатывает вещи аналогично ASP.NET Core, за исключением того, что конфигурация OpenID Connect Connect и подписка на событие `OnAuthorizationCodeReceived` происходят в файле [App_Start \стартуп.АУС.КС](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) . Эти понятия также похожи на ASP.NET Core, за исключением того, что в ASP.NET необходимо указать `RedirectUri` в [файле Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Такая конфигурация немного менее надежна, чем в ASP.NET Core, так как ее необходимо изменить при развертывании приложения.

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

# <a name="javatabjava"></a>[Java](#tab/java)

См. раздел [веб-приложение, которое входит в систему пользователей: конфигурация кода](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) , чтобы понять, как пример кода Java получает код авторизации. После того как приложение получит код, [аусфилтер. Java # L51-l56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Делегирует методу `AuthHelper.processAuthenticationCodeRedirect` в [AuthHelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
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

Метод `getAuthResultByAuthCode` определен в [AuthHelper. Java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Он создает `ConfidentialClientApplication`MSAL, а затем вызывает `acquireToken()` с `AuthorizationCodeParameters`, созданным из кода авторизации.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Будет запрошен поток кода авторизации, как показано в [веб-приложении, которое выполняет вход в систему пользователей: конфигурация кода](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). Затем код получается в функции `authorized`, которая Flask маршруты из URL-адреса `/getAToken`. Полный контекст этого кода см. в разделе [app. Корректировка # L30-l44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) .

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

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

В руководстве по ASP.NET Core используется внедрение зависимостей, позволяющее выбрать реализацию кэша маркеров в файле Startup.cs для приложения. Microsoft. Identity. Web поставляется с предварительно созданными сериализаторами кэша маркеров, описанными в разделе [сериализация кэша маркеров](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Интересной возможностью является выбор ASP.NET Core [распределенных кэшей памяти](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache):

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
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

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

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

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java предоставляет методы для сериализации и десериализации кэша маркеров. Пример Java обрабатывает сериализацию из сеанса, как показано в методе `getAuthResultBySilentFlow` в [AuthHelper. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

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

Подробные сведения о классе `SessionManagementHelper` приведены в [примере MSAL для Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="pythontabpython"></a>[Python](#tab/python)

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

## <a name="next-steps"></a>Дальнейшие действия

На этом этапе при входе пользователя маркер сохраняется в кэше маркеров. Давайте посмотрим, как он используется в других частях веб-приложения.

> [!div class="nextstepaction"]
> [Веб-приложение, вызывающее веб-API: удаление учетных записей из кэша при глобальном выходе](scenario-web-app-call-api-sign-in.md)
