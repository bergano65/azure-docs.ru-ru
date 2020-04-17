---
title: Назначаем веб-приложение, которое вызывает веб-AI - идентификационная платформа Microsoft Azure
description: Узнайте, как настроить код веб-приложения, которое вызывает web-аПО
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
ms.openlocfilehash: 7f05d33b43df85c49a0c92b60157e2a6448325ac
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537140"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Веб-приложение, которое вызывает web-aIS: конфигурация кода

Как показано в [веб-приложении, которое подписывает в](scenario-web-app-sign-user-overview.md) сценарии пользователей, веб-приложение использует [поток кода авторизации OAuth 2.0](v2-oauth2-auth-code-flow.md) для регистрации пользователя. Этот поток имеет два этапа:

1. Запросить код авторизации. Эта часть делегирует частный диалог с пользователем на платформу идентификации Майкрософт. В ходе этого диалога пользователь вписывается и соглашается на использование web-aIS. Когда частный диалог заканчивается успешно, веб-приложение получает код авторизации на его перенаправление URI.
1. Запросите токен доступа для API, искупив код авторизации.

[Веб-приложение, которое подписывает в](scenario-web-app-sign-user-overview.md) сценариях пользователей, охватывает только первый шаг. Здесь вы узнаете, как изменить ваше веб-приложение так, чтобы оно не только подписывало пользователей, но и теперь вызывает веб-AIS.

## <a name="libraries-that-support-web-app-scenarios"></a>Библиотеки, поддерживающие сценарии веб-приложений

Следующие библиотеки в Библиотеке подлинности Майкрософт (MSAL) поддерживают поток кода авторизации для веб-приложений:

| Библиотека MSAL | Описание |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Поддержка платформ .NET Framework и .NET Core. Не поддерживаются Универсальные платформы Windows (UWP), Xamarin.iOS, и Xamarin.Android, потому что эти платформы используются для создания общедоступных приложений клиента. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL для Python | Поддержка веб-приложений Python. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL для Java | Поддержка веб-приложений Java. |

Выберите вкладку для интересуемых вами платформы:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Поскольку пользователь, всданный в систему Open ID connect (OIDC), делегируется промежуточному программному обеспечению Open ID connect (OIDC), необходимо взаимодействовать с процессом OIDC. Способ взаимодействия зависит от используемой инфраструктуры.

Для ASP.NET Core вы подпишитесь на события промежуточного посуды OIDC:

- Вы позволите ASP.NET Core запросить код авторизации с помощью промежуточного программного обеспечения Open ID Connect. ASP.NET или ASP.NET Core позволит пользователю войти и дать согласие.
- Вы подпишитесь на веб-приложение, чтобы получить код авторизации. Эта подписка выполняется с помощью делегата СК.
- Когда код авторизации будет получен, вы будете использовать библиотеки MSAL, чтобы выкупить его. Полученные токены доступа и маркеры обновления хранятся в кэше маркеров. Кэш может использоваться в других частях приложения, таких как контроллеры, для бесшумного приобретения других токенов.

Примеры кода в этой статье и следующий из них извлекаются из [ASP.NET Core веб-приложение инкрементный учебник, глава 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Вы можете сослаться на этот учебник для получения полной информации о реализации.

> [!NOTE]
> Чтобы полностью понять примеры кода здесь, вы должны быть знакомы с [ASP.NET основных основ,](https://docs.microsoft.com/aspnet/core/fundamentals)и, в [частности, с инъекцией зависимости](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) и [вариантов.](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Поскольку пользователь, всданный в систему Open ID connect (OIDC), делегируется промежуточному программному обеспечению Open ID connect (OIDC), необходимо взаимодействовать с процессом OIDC. Способ взаимодействия зависит от используемой инфраструктуры.

Для ASP.NET вы подпишитесь на промежуточное программное обеспечение oidC событий:

- Вы позволите ASP.NET Core запросить код авторизации с помощью промежуточного программного обеспечения Open ID Connect. ASP.NET или ASP.NET Core позволит пользователю войти и дать согласие.
- Вы подпишитесь на веб-приложение, чтобы получить код авторизации. Эта подписка выполняется с помощью делегата СК.
- Когда код авторизации будет получен, вы будете использовать библиотеки MSAL, чтобы выкупить его. Полученные токены доступа и маркеры обновления хранятся в кэше маркеров. Кэш может использоваться в других частях приложения, таких как контроллеры, для бесшумного приобретения других токенов.

Примеры кода в этой статье и следующий из них извлекаются из [образца веб-приложений ASP.NET.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) Возможно, вы захотите сослаться на этот пример для получения полной информации о реализации.

# <a name="java"></a>[Java](#tab/java)

Примеры кода в этой статье и следующий из них извлекаются из [веб-приложения Java, которое называется Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), образец веб-приложения, который использует MSAL для Java.
В настоящее время образец позволяет MSAL для Java создавать URL-адрес авторизации кода и обрабатывает навигацию до точки авторизации для платформы идентификации Майкрософт. Также можно использовать безопасность Sprint для входиныго вхасывая. Возможно, вы захотите сослаться на образец для получения полной информации о реализации.

# <a name="python"></a>[Python](#tab/python)

Примеры кода в этой статье и следующий из них извлекаются из [веб-приложения Python, вызывая Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), образец веб-приложения, который использует MSAL. Python.
Образец в настоящее время позволяет MSAL. Python производит URL-адрес авторизации кода и обрабатывает навигацию до точки авторизации для платформы идентификации Майкрософт. Возможно, вы захотите сослаться на образец для получения полной информации о реализации.

---

## <a name="code-that-redeems-the-authorization-code"></a>Код, который высылает код авторизации

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

В ASP.NET Core, `Startup.cs` в файле, `OnAuthorizationCodeReceived` вы подписываетесь на событие OpenID Connect. Из этого события позвоните в метод MSAL.NET. `AcquireTokenFromAuthorizationCode` Этот метод хранит следующие маркеры в кэше токенов:

- *Токен доступа* для запрошенного `scopes`.
- *Обновленный маркер*. Этот маркер будет использоваться для обновления маркера доступа, когда он близок к истечению, или для получения другого токена от имени того же пользователя, но для другого ресурса.

[Учебник ASP.NET Core Web-приложений](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) предоставляет вам многоразовый код для веб-приложений.

Ниже приводится код от [Startup.cs'L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42). Он имеет звонки:

- Метод, `AddMicrosoftIdentityPlatformAuthentication` который добавляет аутентификацию в веб-приложение.
- Метод, `AddMsal` который добавляет возможность вызова веб-AIS.
- Метод, `AddInMemoryTokenCaches` который заключается в выборе реализации кэша маркеров.

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

`Constants.ScopeUserRead`определено в [Constants.cs'L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5):

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Вы уже изучили содержание `AddMicrosoftIdentityPlatformAuthentication` в [Web-приложении, которое подписывает в пользователях - конфигурация кода.](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code)

### <a name="the-addmsal-method"></a>Метод AddMsal

Код для `AddMsal` находится в [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs-L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

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

Метод `AddMsal` гарантирует, что:

- Веб-приложение ASP.NET Core запрашивает как токен ID для`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`пользователя, так и код проверки подлинности ().
- Область `offline_access` добавляется. Эта область получает согласие пользователя на получение токена обновления.
- Приложение подписывается на `OnAuthorizationCodeReceived` событие OIDC и высылает вызов с помощью MSAL.NET, которая инкапсулируется здесь в многоразовый компонент реализации. `ITokenAcquisition`

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>Метод TokenAcquisition.AddAccountToCacheFromАвторизацияКодеАсинкик

Метод `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` расположен на сайте [Microsoft.Identity.Web/TokenAcquisition.cs-L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Это гарантирует, что:

- ASP.NET не пытается выкупить код проверки подлинности`context.HandleCodeRedemption();`параллельно MSAL.NET ().
- Претензии в маркере ID доступны для MSAL для вычисления ключа кэша маркеров для учетной записи пользователя.
- При необходимости создается экземпляр приложения MSAL.NET.
- Код выкуплен MSAL.NET приложением.
- Новый токен ID совместно с ASP.NET `context.HandleCodeRedemption(null, result.IdToken);`Core во время вызова. Токен доступа не передается ASP.NET Core. Он остается в MSAL.NET кэше маркеров, связанных с пользователем, где он готов к использованию в ASP.NET контроллеров Core.

Вот соответствующий код `TokenAcquisition`для:

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

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>Метод tokenAcquisition.BuildConfidentialClientApplication

В ASP.NET Core, здание конфиденциального клиентского приложения `HttpContext`использует информацию, которая в . Доступ `HttpContext` к запросу осуществляется с `CurrentHttpContext` помощью свойства. `HttpContext`имеет информацию о URL для веб-приложения и о `ClaimsPrincipal`вписав в пользователя (в ).

Метод `BuildConfidentialClientApplication` также использует конфигурацию ASP.NET Core. Конфигурация имеет раздел "AzureAD", а также связана с обоими следующими элементами:

- Структура `_applicationOptions` данных типа [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet).
- Экземпляр `azureAdOptions` типа [AzureAdOptions,](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs)определенный `Authentication.AzureAD.UI`в ASP.NET Core .

Наконец, приложение должно поддерживать кэши маркеров. Подробнее об этом вы узнаете в следующем разделе.

Код метода `GetOrBuildConfidentialClientApplication()` находится в [Microsoft.Identity.Web/TokenAcquisition.cs-L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Он использует членов, которые были введены инъекции зависимости (прошел в конструктор `TokenAcquisition` евразийцев в [Microsoft.Identity.Web/TokenAcquisition.cs'L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)).

Вот код для: `GetOrBuildConfidentialClientApplication`

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

`AcquireTokenByAuthorizationCode`это действительно метод, который высылает код авторизации, который ASP.NET запросов, и который получает маркеры, которые добавляются в MSAL.NET кэша маркеров пользователя. Из кэша токены затем используются в контроллерах ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET обрабатывает вещи так же, как ASP.NET Core, за исключением `OnAuthorizationCodeReceived` того, что конфигурация OpenID Connect и подписка на событие происходят в [файле App_Start-Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) Понятия также аналогичны тем, которые ASP.NET Core, за исключением `RedirectUri` того, что в ASP.NET вы должны указать в [Web.config-L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Эта конфигурация немного менее надежна, чем в ASP.NET Core, потому что при развертывании приложения необходимо изменить ее.

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

Просмотрите [веб-приложение, которое подписывает сяре в пользователях: конфигурация кода,](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) чтобы понять, как образец Java получает код авторизации. После того, как приложение получает код, [AuthFilter.java-L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Делегаты `AuthHelper.processAuthenticationCodeRedirect` метода в [AuthHelper.java-L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
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

Метод `getAuthResultByAuthCode` определен в [AuthHelper.java-L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Он создает MSAL, `ConfidentialClientApplication`а `acquireToken()` `AuthorizationCodeParameters` затем вызывает с созданным из кода авторизации.

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

Поток кода авторизации запрашивается, как показано в [веб-приложении, которое подписывается в пользователях: конфигурация кода.](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code) Код затем получается `authorized` на функции, которая `/getAToken` Колба маршрутов из URL. Смотрите [приложение.py-L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) для полного контекста этого кода:

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

Вместо секрета клиента, конфиденциальное приложение клиента может также доказать свою личность, используя сертификат клиента, или утверждение клиента.
Использование утверждений клиентов является продвинутым сценарием, подробно описанным в [утверждениях Клиента.](msal-net-client-assertions.md)

## <a name="token-cache"></a>Кэш маркеров

> [!IMPORTANT]
> Реализация токен-кэша для веб-приложений или web-aI-приложений отличается от реализации для настольных приложений, которые часто [основаны на файлах.](scenario-desktop-acquire-token.md#file-based-token-cache)
> По соображениям безопасности и производительности важно обеспечить наличие одного кэша токенов на учетную запись пользователя. Необходимо выставить кэш маркеров для каждой учетной записи.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

В ASP.NET основной учебник использует инъекцию зависимости, чтобы вы могли решить, как реализовать кэш маркеров в Startup.cs файле для приложения. Microsoft.Identity.Web поставляется с предварительно построенными серийными кэшами маркеров, описанными в [сериализации кэша токенов.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization) Интересная возможность заключается в выборе ASP.NET основных [распределенных кэшов памяти:](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)

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

Подробную информацию о поставщиках кэша токенов можно узнать также в [учебниках по веб-приложению core ASP.NET Core Этап кэша маркеров](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) в учебнике.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Реализация токен-кэша для веб-приложений или web-aI-приложений отличается от реализации для настольных приложений, которые часто [основаны на файлах.](scenario-desktop-acquire-token.md#file-based-token-cache)

Реализация веб-приложения может использовать ASP.NET сеанс или память сервера. Например, посмотрите, как реализация кэша подключается после создания приложения MSAL.NET в [msalAppBuilder.cs-L39-L51:](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)

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

MSAL Java предоставляет методы сериализации и десеризации кэша маркеров. Образец Java обрабатывает сериализацию из сеанса, `getAuthResultBySilentFlow` как показано в методе в [AuthHelper.java-L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

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

Подробная информация `SessionManagementHelper` о классе приведена в [образце MSAL для Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="python"></a>[Python](#tab/python)

В образце Python один кэш на учетную запись обеспечивается путем воссоздания конфиденциального клиентского приложения для каждого запроса, а затем его сериализации в кэше сеанса фляг:

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

В этот момент, когда пользователь впишется, маркер хранится в кэше маркера. Давайте посмотрим, как он затем используется в других частях веб-приложения.

> [!div class="nextstepaction"]
> [Веб-приложение, которое вызывает web-aI: Удалить учетные записи из кэша на глобальном вывески](scenario-web-app-call-api-sign-in.md)
