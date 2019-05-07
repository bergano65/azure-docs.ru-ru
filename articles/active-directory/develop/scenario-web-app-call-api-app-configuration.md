---
title: Веб-приложения, что вызовы веб-API (Настройка кода) — платформе Microsoft identity
description: Узнайте, как создать веб-приложение, вызовы веб-API-интерфейсы (конфигурации кода приложения)
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
ms.openlocfilehash: 95a5e1ed89b6330a0b6a49cb20d8bf0ef3587d48
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074745"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Веб-приложения, что вызовы веб-интерфейсы API — код конфигурации

Как видно из [сценарий входа в систему пользователей веб-приложения](scenario-web-app-sign-user-overview.md)при условии, что вход пользователя делегируется Open ID connect (OIDC) по промежуточного слоя, чтобы ловушка вверх в процессе OIDC. Способ сделать это различается в зависимости от платформы вы использовали (здесь ASP.NET и ASP.NET Core), но в итоге будет подписаться на события по промежуточного слоя OIDC. Принцип является то, что:

- Вы разрешаете ASP.NET или ASP.NET core запрос кода авторизации. Во время работы этого ASP.NET/ASP.NET core сообщит пользователю вход и с согласия пользователя
- Веб-приложением, будет подписаться на получение кода авторизации.
- Получив код проверки подлинности, вы используете библиотеки MSAL для активировать код и полученных маркеров доступа и обновите хранилище токенов в кэше токенов. После этого кэша можно использовать в других частях приложения для получения других маркеров без вмешательства пользователя.

## <a name="libraries-supporting-web-app-scenarios"></a>Библиотеки, поддерживая сценарии веб-приложения

Ниже приведены библиотеки, которые поддерживают поток кода авторизации для веб-приложений.

| Библиотека MSAL | ОПИСАНИЕ |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Поддерживаемыми платформами являются платформы .NET Framework и .NET Core (не UWP, Xamarin.iOS и Xamarin.Android, как эти платформы используются для построения общедоступных клиентских приложений) |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Разработка решений в ход выполнения — в общедоступной предварительной версии |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL. Java | Разработка решений в ход выполнения — в общедоступной предварительной версии |

## <a name="aspnet-core-configuration"></a>Конфигурации ASP.NET Core

В ASP.NET Core, выполняются действия в `Startup.cs` файл. Стоит подписаться на `OnAuthorizationCodeReceived` открыть Идентификатором мероприятие connect, а из этого события, вызовите MSAL. Метод NET `AcquireTokenFromAuthorizationCode` которого действует хранение в кэше маркера, маркер доступа для запрошенной области и маркер обновления, который будет использоваться для обновления маркера доступа, когда оно находится рядом истечения срока действия, чтобы получить маркер от имени одного пользователя , но для другого ресурса.

Комментарии в коде ниже, поможет вам понять некоторые аспекты непросто weaving MSAL.NET и ASP.NET Core

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add(OidcConstants.ScopeOfflineAccess);
   options.Scope.Add("user.read"); // for instance

   // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
   // where it will be usable from Controllers later (through the TokenAcquisition service)
   var handler = options.Events.OnAuthorizationCodeReceived;
   options.Events.OnAuthorizationCodeReceived = async context =>
   {
    // As AcquireTokenByAuthorizationCode is asynchronous we want to tell ASP.NET core
    // that we are handing the code even if it's not done yet, so that it does 
    // not concurrently call the Token endpoint.
    context.HandleCodeRedemption();

    // Call MSAL.NET AcquireTokenByAuthorizationCode
    var application = BuildConfidentialClientApplication(context.HttpContext,
                                                         context.Principal);
    var result = await application.AcquireTokenByAuthorizationCode(scopes.Except(scopesRequestedByMsalNet), context.ProtocolMessage.Code)
                                  .ExecuteAsync();

    // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it
    // and will not send the OAuth 2.0 request in case a further call to
    // AcquireTokenByAuthorizationCodeAsync in the future for incremental consent 
    // (getting a code requesting more scopes)
    // Share the ID Token so that the identity of the user is known in the application (in 
    // HttpContext.User)
    context.HandleCodeRedemption(null, result.IdToken);

    // Call the previous handler if any
    await handler(context);
   };
```

В ASP.NET Core конфиденциальные клиентское приложение использует данные, в HttpContext. Этот HttpContext знает об URL-адрес для веб-приложения, а также вошедшего в систему пользователя (в `ClaimsPrincipal`). Он также использует конфигурации ASP.NET Core, который имеет раздел «AzureAD», и который связан с `_applicationOptions` структуру данных. Наконец, приложение должно поддерживать кэши маркеров.

```CSharp
/// <summary>
/// Creates an MSAL Confidential client application
/// </summary>
/// <param name="httpContext">HttpContext associated with the OIDC response</param>
/// <param name="claimsPrincipal">Identity for the signed-in user</param>
/// <returns></returns>
private IConfidentialClientApplication BuildConfidentialClientApplication(HttpContext httpContext, ClaimsPrincipal claimsPrincipal)
{
 var request = httpContext.Request;

 // Find the URI of the application)
 string currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, azureAdOptions.CallbackPath ?? string.Empty);

 // Updates the authority from the instance (including national clouds) and the tenant
 string authority = $"{azureAdOptions.Instance}{azureAdOptions.TenantId}/";

 // Instantiates the application based on the application options (including the client secret)
 var app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
               .WithRedirectUri(currentUri)
               .WithAuthority(authority)
               .Build();

 // Initialize token cache providers. In the case of Web applications, there must be one
 // token cache per user (here the key of the token cache is in the claimsPrincipal which
 // contains the identity of the signed-in user)
 if (this.UserTokenCacheProvider != null)
 {
  this.UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (this.AppTokenCacheProvider != null)
 {
  this.AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

`AcquireTokenByAuthorizationCode` действительно активирует код авторизации, автор запроса ASP.NET и получает маркеры, которые добавляются в кэш маркеров MSAL.NET пользователя. После этого они затем используется, контроллеры ASP.NET Core.

## <a name="aspnet-configuration"></a>Конфигурация ASP.NET

Способ ASP.NET обрабатывает действия аналогичен предыдущему, за исключением того, что конфигурация OpenIdConnect и подписка на `OnAuthorizationCodeReceived` событие происходит в `App_Start\Startup.Auth.cs` файл. Вы увидите подобные концепции, за исключением того, что здесь нужно будет указать URI перенаправления в файле конфигурации, который является менее надежной:

```CSharp
private void ConfigureAuth(IAppBuilder app)
{
 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

 app.UseCookieAuthentication(new CookieAuthenticationOptions { });

 app.UseOpenIdConnectAuthentication(
 new OpenIdConnectAuthenticationOptions
 {
  Authority = Globals.Authority,
  ClientId = Globals.ClientId,
  RedirectUri = Globals.RedirectUri,
  PostLogoutRedirectUri = Globals.RedirectUri,
  Scope = Globals.BasicSignInScopes, // a basic set of permissions for user sign in & profile access
  TokenValidationParameters = new TokenValidationParameters
  {
  // We'll inject our own issuer validation logic below.
  ValidateIssuer = false,
  NameClaimType = "name",
  },
  Notifications = new OpenIdConnectAuthenticationNotifications()
  {
   AuthorizationCodeReceived = OnAuthorizationCodeReceived,
  }
 });
}
```

```CSharp
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
{
 // Upon successful sign in, get & cache a token using MSAL
 string userId = context.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
 IConfidentialClientApplication clientapp;
 clientApp = ConfidentialClientApplicationBuilder.Create(Globals.ClientId)
                  .WithClientSecret(Globals.ClientSecret)
                  .WithRedirectUri(Globals.RedirectUri)
                  .WithAuthority(new Uri(Globals.Authority))
                  .Build();

  EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);

 AuthenticationResult result = await clientapp.AcquireTokenByAuthorizationCode(scopes, context.Code)
                                              .ExecuteAsync();
}
```

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>Токен MSAL.NET кэша для приложения веб-ASP.NET (ядра)

В веб-приложений (или веб-API, что это случится), реализацию кэша маркеров отличается от реализации кэша маркера настольных приложений (которые часто являются [файла](scenario-desktop-acquire-token.md#file-based-token-cache). Его можно использовать сеанс ASP.NET/ASP.NET Core, кэш Redis, или базу данных или даже хранилище BLOB-объектов Azure. В коде фрагмент кода выше это — объект `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` вызов метода, который привязывает службы кэша. Сведения о том, что произойдет, здесь выходит за рамки данного руководства сценария, но ниже приводятся ссылки.

> [!IMPORTANT]
> — Это очень важно осознавать, что для веб-приложений и веб-API, должен быть один кэш маркеров на пользователя (в учетной записи). Необходимо выполнить сериализацию кэша маркера для каждой учетной записи.

Примеры того, как использовать кэш маркеров для веб-приложений и веб-API доступны в [руководство по приложениям ASP.NET Core Web](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) на этапе [кэш маркеров 2-2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). Для реализации взглянем на следующую папку [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) в [microsoft-проверка подлинности расширения for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) библиотеки (в [ Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) папки.

## <a name="next-steps"></a>Дальнейшие действия

На этом этапе когда пользователь выполняет вход маркера хранится в кэш маркеров. Давайте посмотрим, как он затем используется в других частях веб-приложения.

> [!div class="nextstepaction"]
> [Войдите в веб-приложение](scenario-web-app-call-api-sign-in.md)
