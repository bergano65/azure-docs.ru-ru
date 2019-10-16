---
title: Веб-приложение, вызывающее веб-API (конфигурация кода) — платформа Microsoft Identity
description: Узнайте, как создать веб-приложение, вызывающее веб-API (конфигурация кода приложения).
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1131cba204b7b7af33cc0441ee455b6e333aba20
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310081"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Веб-приложение, вызывающее веб-API — конфигурация кода

Как видно в сценарии "вход [пользователей в веб-приложение](scenario-web-app-sign-user-overview.md)", веб-приложение использует [поток кода авторизации](v2-oauth2-auth-code-flow.md) OAuth 2.0 для входа пользователя. Этот поток состоит из двух частей:

1. Запросите код авторизации. Эта часть делегирует пользователю диалоговое окно частного диалога платформы идентификации Майкрософт. Пользователь входит в систему и задается при использовании веб-API. Когда это частное диалоговое окно завершается успешно, приложение получает код авторизации в URI перенаправления.
1. Запросите маркер доступа для API с помощью активации кода авторизации.

[Сценарий входа в веб-приложение —](scenario-web-app-sign-user-overview.md) это только первая Разрезка. Узнайте, как изменить веб-API, который будет выполнять вход пользователей, чтобы он вызывал веб-API.

## <a name="libraries-supporting-web-app-scenarios"></a>Библиотеки, поддерживающие сценарии веб-приложений

Библиотеки, поддерживающие поток кода авторизации для веб-приложений:

| Библиотека MSAL | Описание |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Поддерживаемыми платформами являются .NET Framework и платформы .NET Core (не UWP, Xamarin. iOS и Xamarin. Android, так как эти платформы используются для создания общедоступных клиентских приложений). |
| ![MSAL. Языке](media/sample-v2-code/logo_python.png) <br/> MSAL. Языке | Выполняется разработка — в общедоступной предварительной версии |
| ![MSAL. Java](media/sample-v2-code/logo_java.png) <br/> MSAL. Java | Выполняется разработка — в общедоступной предварительной версии |

Выберите вкладку, соответствующую интересующей вас платформе:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Учитывая, что разрешение на вход пользователя делегируется по промежуточного слоя Open ID Connect (OIDC), необходимо подключить процесс OIDC. Это отличается в зависимости от используемой платформы.
В случае ASP.NET Core вы будете подписываться на события OIDC по промежуточного слоя. Принцип состоит в том, что:

- Вы разрешите ASP.NET Core запросить код авторизации с помощью по промежуточного слоя Open ID Connect. Выполняя эту ASP.NET/ASP.NET ядро, вы сможете войти в систему и дать согласие на вход,
- Вы будете подписываться на получение кода авторизации веб-приложением. Эта подписка выполняется через C# делегат.
- После получения кода проверки подлинности вы будете использовать библиотеки MSAL для активации кода, а результирующие маркеры доступа и маркеры обновления хранятся в кэше маркеров. В этом случае кэш можно использовать в других частях приложения, например в контроллерах, для получения других маркеров без вмешательства пользователя.

Фрагменты кода в этой статье и следующие извлекаются из [пошагового руководства по ASP.NET Core веб-приложению, главе 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Для получения подробных сведений о реализации может потребоваться обратиться к этому учебнику.

> [!NOTE]
> Чтобы полностью разобраться с фрагментами кода, необходимо ознакомиться с [ASP.NET Core фундаментальными принципами](https://docs.microsoft.com/aspnet/core/fundamentals), а также с определенным [внедрением](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) и [параметрами](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) зависимостей.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Учитывая, что разрешение на вход пользователя делегируется по промежуточного слоя Open ID Connect (OIDC), необходимо подключить процесс OIDC. Это отличается в зависимости от используемой платформы.
В случае с ASP.NET вы будете подписываться на события промежуточного слоя OIDC. Принцип состоит в том, что:

- Вы разрешите ASP.NET Core запросить код авторизации с помощью по промежуточного слоя Open ID Connect. Выполняя эту ASP.NET/ASP.NET ядро, вы сможете войти в систему и дать согласие на вход,
- Вы будете подписываться на получение кода авторизации веб-приложением. Это C# делегат.
- После получения кода проверки подлинности вы будете использовать библиотеки MSAL для активации кода. Результирующие маркеры доступа и маркеры обновления, а затем хранятся в кэше маркеров. В этом случае кэш можно использовать в других частях приложения, например в контроллерах, для получения других маркеров без вмешательства пользователя.

Фрагменты кода в этой статье и следующие извлекаются из [примера веб-приложения ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Для получения полной информации о реализации можно использовать этот пример.

# <a name="javatabjava"></a>[Java](#tab/java)

Фрагменты кода в этой статье и следующие извлекаются из примера веб [-приложения Java, вызывающего](https://github.com/Azure-Samples/ms-identity-java-webapp) веб-приложение Microsoft Graph msal4j.
В настоящее время пример позволяет msal4j создать URL-адрес кода авторизации и обрабатывать навигацию до конечной точки авторизации платформы Microsoft Identity. Можно также использовать безопасность спринта для входа пользователя. Для получения полной информации о реализации можно использовать этот пример.

# <a name="pythontabpython"></a>[Python](#tab/python)

Фрагменты кода в этой статье и следующие извлекаются из [веб-приложения Python, обращающегося к Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) MSAL. Пример веб-приложения Python.
В настоящее время пример позволяет MSAL. Python создает URL-адрес кода авторизации и обрабатывает навигацию до конечной точки авторизации платформы Microsoft Identity. Для получения полной информации о реализации можно использовать этот пример.

---

## <a name="code-that-redeems-the-authorization-code"></a>Код, который активирует код авторизации

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

В ASP.NET Core принцип состоит в том, что в `Startup.cs` файле. Вам нужно оформить подписываться на `OnAuthorizationCodeReceived` событие Open ID Connect и из этого события вызвать MSAL. Метод `AcquireTokenFromAuthorizationCode`NET, который оказывает воздействие на сохранение в кэше маркеров, маркер доступа для запрошенного `scopes`и маркер обновления, который будет использоваться для обновления маркера доступа, когда он близок к истечению срока действия, или для получения маркера от имени того же пользователя. , но для другого ресурса.

На практике [учебник по ASP.NET Core веб-приложению](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) пытается предоставить вам повторно используемый код для веб-приложений.

Ниже приведен код [Startup. CS # L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42) , в котором используется вызов `AddMicrosoftIdentityPlatformAuthentication` метода, который добавляет проверку подлинности в веб-приложение и `AddMsal` добавляет возможность вызова веб-API. Вызов метода заключается `AddInMemoryTokenCaches` в выборе реализации кэша маркеров среди возможных вариантов.

```CSharp
public class Startup
{
  // Code not show here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not show here
}
```

`Constants.ScopeUserRead`определяется в [константах. CS # 5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)

```CSharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Вы уже изсмотрели содержимое `AddMicrosoftIdentityPlatformAuthentication` в [веб-приложении, которое входит в систему пользователей — конфигурация кода](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code)

### <a name="the-addmsal-method"></a>Метод Аддмсал

Код для `AddMsal` находится в [Microsoft. Identity. Web/вебаппсервицеколлектионекстенсионс. CS # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```CSharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization.
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the Web App or Web API
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign-in with their Microsoft personal accounts
          // (it's required by MSAL.NET and automatically provided when users sign-in with work or school accounts)
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

          // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
          // where it will be usable from Controllers later (through the TokenAcquisition service)
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

`AddMsal` Метод гарантирует следующее:

- Веб-приложение ASP.NET Core запрашивает как IDToken для пользователя, так и код проверки подлинности (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`).
- `offline_access` область добавляется. Это необходимо, чтобы пользователь предоставил возможность получить маркер обновления для приложения.
- Приложение подписывается на событие OIDC `OnAuthorizationCodeReceived` и активирует вызов с помощью MSAL.NET, который инкапсулирован в повторно используемый компонент, реализующий. `ITokenAcquisition`

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>Метод Токенаккуиситион. Аддаккаунттокачефромаусоризатионкодеасинк

Метод находится в [Microsoft. Identity. Web/токенаккуиситион. CS # L101-L145.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145) `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` Это гарантирует, что:

- ASP.NET не пытается активировать код проверки подлинности параллельно с MSAL.NET`context.HandleCodeRedemption();`()
- Утверждения в IDToken доступны для MSAL, чтобы вычислить ключ кэша маркера для учетной записи пользователя.
- При необходимости создается экземпляр приложения MSAL.NET
- код активируется приложением MSAL.NET
- Новый маркер идентификатора используется совместно с ASP.NET Core (во время вызова `context.HandleCodeRedemption(null, result.IdToken);`). Маркер доступа не используется совместно с ASP.NET Core. Он остается в кэше маркеров MSAL.NET, связанном с пользователем, где он готов к использованию в контроллерах ASP.NET Core.

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
      // As AcquireTokenByAuthorizationCodeAsync is asynchronous we want to tell ASP.NET core that we are handing the code
      // even if it's not done yet, so that it does not concurrently call the Token endpoint. (otherwise there will be a
      // race condition ending-up in an error from Azure AD telling "code already redeemed")
      context.HandleCodeRedemption();

      // The cache will need the claims from the ID token.
      // If they are not yet in the HttpContext.User's claims, so adding them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it and will not send the OAuth 2.0 request in
      // case a further call to AcquireTokenByAuthorizationCodeAsync in the future is required for incremental consent (getting a code requesting more scopes)
      // Share the ID Token though
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

В ASP.NET Core сборка конфиденциального клиентского приложения использует сведения, содержащиеся в HttpContext. Доступ к ним `CurrentHttpContext` осуществляется с помощью свойства, HttpContext, связанного с запросом, известно о URL-адресе приложения и пользователя, выполнившего вход ( `ClaimsPrincipal`в). В `BuildConfidentialClientApplication` также используется конфигурация ASP.NET Core, которая содержит раздел "AzureAD", который привязан и к:

- Структура данных типа [конфидентиалклиентаппликатионоптионс](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet) `_applicationOptions`
- экземпляр `azureAdOptions` типа [азуреадоптионс](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) , определенный в ASP.NET Core `Authentication.AzureAD.UI`. Наконец, приложению необходимо поддерживать кэши маркеров. Дополнительные сведения об этом см. в следующем разделе.

Код для `GetOrBuildConfidentialClientApplication()` метода находится в [Microsoft. Identity. Web/токенаккуиситион. CS # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). В нем используются члены, которые были добавлены путем внедрения зависимостей (передаются в конструкторе Токенаккуиситион в [Microsoft. Identity. Web/токенаккуиситион. CS # L47-L59).](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)

```CSharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL Confidential client application if needed
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

Для суммирования `AcquireTokenByAuthorizationCode` фактически активирует код авторизации, запрошенный ASP.NET, и получает маркеры, которые добавляются в кэш пользовательских маркеров MSAL.NET. После этого они будут использоваться в контроллерах ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET обрабатывает вещи так же, как и ASP.NET Core, за исключением того, что конфигурация OpenIdConnect и подписка на `OnAuthorizationCodeReceived` событие происходит в файле [App_Start\Startup.auth.CS](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) . Аналогичные понятия можно найти в ASP.NET Core, за исключением того, что в ASP.NET необходимо указать RedirectUri в [файле Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Такая конфигурация немного менее надежна, чем та, которая выполняется в ASP.NET Core, так как ее потребуется изменить при развертывании приложения.

```CSharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the querystring (&code=<code>).
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
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // a basic set of permissions for user sign in & profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application you would use IssuerValidator for additional checks, like making sure the user's organization has signed up for your app.
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
      // Upon successful sign in, get the access token & cache it using MSAL
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

Сведения о том, как образец Java получает код авторизации, см. в разделе [веб-приложение, которое выполняет вход пользователей — конфигурация кода](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) . После получения приложением [аусфилтер. Java # L51-l56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56) делегирует `AuthHelper.processAuthenticationCodeRedirect` методу в [AuthHelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97), а затем вызывает `getAuthResultByAuthCode`:

```Java
class AuthHelper {
  // code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// code omitted
  }
}
```

Метод определен в [AuthHelper. Java # L176.](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176) `getAuthResultByAuthCode` Он создает MSAL `ConfidentialClientApplication` и вызывает метод `acquireToken()` , `AuthorizationCodeParameters` созданный из кода авторизации.

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

После запроса потока кода авторизации, как показано в [веб-приложении, которое выполняет вход пользователей — конфигурация кода](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code), код авторизации получается в `authorized` функции, Flask маршруты из URL-адреса/жетатокен. См [. раздел App. Корректировка # L30-l44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Вместо секрета клиента конфиденциальное клиентское приложение может также подтвердить свою личность с помощью сертификата клиента или утверждения клиента.
Использование утверждений клиента является расширенным сценарием, подробно описанным в [проверочных утверждениях клиента](msal-net-client-assertions.md) .

## <a name="token-cache"></a>Кэш маркеров

> [!IMPORTANT]
> В веб-приложениях (или веб-API) реализация кэша маркеров отличается от реализации кэша маркеров настольных приложений (которые часто [основаны на файлах](scenario-desktop-acquire-token.md#file-based-token-cache)).
> Это важно для обеспечения безопасности и производительности, чтобы гарантировать, что для веб-приложений и веб-API требуется один кэш маркеров на пользователя (на учетную запись). Сериализацию кэша маркеров также следует выполнять отдельно для каждой учетной записи.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

В руководстве по ASP.NET Core используется внедрение зависимостей, позволяющее выбрать реализацию кэша маркеров в файле Startup.cs для приложения. Microsoft. Identity. Web поставляется с несколькими предварительно созданными сериализаторами кэша маркеров, описанными в разделе [сериализация кэша маркеров](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Интересной возможностью является выбор ASP.NET Core [распределенных кэшей памяти](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache):

```csharp
// or use a distributed Token Cache by adding
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Дополнительные сведения о поставщиках кэша маркеров см. также в [учебниках по веб-приложениям ASP.NET Core | Стадия работы с кэшами маркеров](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) учебника

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

В веб-приложениях (или веб-API) реализация кэша маркеров отличается от реализации кэша маркеров настольных приложений (которые часто [основаны на файлах](scenario-desktop-acquire-token.md#file-based-token-cache)). Он может использовать сеанс ASP.NET или память сервера. См. статью как привязать реализацию кэша после создания приложения MSAL.NET в [мсалаппбуилдер. CS # l39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)

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
  
      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);
  
      return clientapp;
  }
```

# <a name="javatabjava"></a>[Java](#tab/java)

msal4j предоставляет методы для сериализации и десериализации кэша маркеров. Пример Java обрабатывает сериализацию из сеанса, как показано в `getAuthResultBySilentFlow` методе в [AuthHelper. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)

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
          Collections.singleton("User.ReadBasic.All"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  //update session with latest token cache
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Подробные сведения `SessionManagementHelper` о классе приведены в [](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)

# <a name="pythontabpython"></a>[Python](#tab/python)

В примере Python в отношении кэша по учетной записи обеспечивается повторное создание конфиденциального клиентского приложения для каждого запроса и его сериализация в кэше сеанса Flask:

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

## <a name="next-steps"></a>Следующие шаги

На этом этапе, когда пользователь входит в маркер, он хранится в кэше маркеров. Давайте посмотрим, как он используется в других частях веб-приложения.

> [!div class="nextstepaction"]
> [Вход в веб-приложение](scenario-web-app-call-api-sign-in.md)
