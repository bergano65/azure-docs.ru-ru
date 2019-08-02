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
ms.openlocfilehash: 391546b4d3ac9ad3674897b39284fdd16e9025a1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562262"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Веб-приложение, вызывающее веб-API — конфигурация кода

Как видно в сценарии входа в [веб-приложение](scenario-web-app-sign-user-overview.md), учитывая, что вход пользователя делегируется по промежуточного слоя Open ID Connect (OIDC), который требуется подключить в процессе OIDC. Это отличается в зависимости от используемой платформы (здесь ASP.NET и ASP.NET Core), но в конце вы будете подписываться на события промежуточного слоя OIDC. Принцип состоит в том, что:

- Вы разрешите ASP.NET или ASP.NET Core запросить код авторизации. Выполняя эту ASP.NET/ASP.NET ядро, вы сможете войти в систему и дать согласие на вход,
- Вы будете подписываться на получение кода авторизации веб-приложением.
- После получения кода проверки подлинности вы будете использовать библиотеки MSAL для активации кода и результирующих маркеров доступа и хранилища маркеров обновления в кэше маркеров. После этого кэш можно использовать в других частях приложения для получения других маркеров автоматически.

> [!NOTE]
> Фрагменты кода из этой статьи извлекаются из следующих примеров на сайте GitHub, которые являются полностью функциональными:
>
> - [Пошаговое руководство по ASP.NET Core веб-приложения](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)
> - [Пример веб-приложения ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)

## <a name="libraries-supporting-web-app-scenarios"></a>Библиотеки, поддерживающие сценарии веб-приложений

Библиотеки, поддерживающие поток кода авторизации для веб-приложений:

| Библиотека MSAL | Описание |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Поддерживаемыми платформами являются .NET Framework и платформы .NET Core (не UWP, Xamarin. iOS и Xamarin. Android, так как эти платформы используются для создания общедоступных клиентских приложений). |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL. Языке | Выполняется разработка — в общедоступной предварительной версии |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL. Java | Выполняется разработка — в общедоступной предварительной версии |

## <a name="aspnet-core-configuration"></a>Конфигурация ASP.NET Core

В ASP.NET Core вещи происходят в `Startup.cs` файле. Вам нужно оформить подписываться на `OnAuthorizationCodeReceived` событие Open ID Connect и из этого события вызвать MSAL. Метод `AcquireTokenFromAuthorizationCode` NET, который оказывает воздействие на сохранение в кэше маркеров, маркер доступа для запрошенного `scopes`и маркер обновления, который будет использоваться для обновления маркера доступа, когда он близок к истечению срока действия, или для получения маркера от имени того же пользователя. , но для другого ресурса.

```CSharp
string[] scopes = new string[]{ "user.read" };
string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };
```

Комментарии в приведенном ниже коде помогут вам разобраться в некоторых незамысловатых аспектах MSAL.NET и ASP.NET Core. Подробные сведения см. в [разделе Пошаговое руководство по ASP.NET Core веб-приложения, глава 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add("offline_access");
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

В ASP.NET Core сборка конфиденциального клиентского приложения использует сведения, содержащиеся в HttpContext. Это `HttpContext` известно о URL-адресе для приложения и вошедшем в него `ClaimsPrincipal`пользователе (в). 

Он также использует конфигурацию ASP.NET Core, которая содержит раздел "AzureAD", который привязан и к:

- Структура данных типа [конфидентиалклиентаппликатионоптионс](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet) `_applicationOptions`
- экземпляр `azureAdOptions` типа [азуреадоптионс](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) , определенный в ASP.NET Core `Authentication.AzureAD.UI`. Наконец, приложению необходимо поддерживать кэши маркеров.

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
 if (UserTokenCacheProvider != null)
 {
  UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (AppTokenCacheProvider != null)
 {
  AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

Дополнительные сведения о поставщиках кэша маркеров см. в [руководствах по веб-приложениям ASP.NET Core | Кэши маркеров](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/455d32f09f4f6647b066ebee583f1a708376b12f/2-WebApp-graph-user/2-2-TokenCache)

> [!NOTE]
> `AcquireTokenByAuthorizationCode`фактически активирует код авторизации, запрошенный ASP.NET, и получает маркеры, которые добавляются в кэш пользовательских маркеров MSAL.NET. После этого они будут использоваться в контроллерах ASP.NET Core.

## <a name="aspnet-configuration"></a>Конфигурация ASP.NET

Метод ASP.NET обрабатывает вещи, за исключением того, что конфигурация OpenIdConnect и подписка на `OnAuthorizationCodeReceived` событие происходит `App_Start\Startup.Auth.cs` в файле. Вы найдете аналогичные концепции, за исключением того, что здесь необходимо указать RedirectUri в файле конфигурации, что немного менее надежно:

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

Наконец, вместо секрета клиента конфиденциальное клиентское приложение также может доказать свою личность с помощью сертификата клиента или утверждения клиента.
Использование утверждений клиента является расширенным сценарием, подробно описанным в [проверочных утверждениях клиента](msal-net-client-assertions.md) .

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>Кэш маркеров MSAL.NET для веб-приложения ASP.NET (Core)

В веб-приложениях (или веб-API) реализация кэша маркеров отличается от реализации кэша маркеров настольных приложений (которые часто [основаны на файлах](scenario-desktop-acquire-token.md#file-based-token-cache)). Он может использовать сеанс ASP.NET/ASP.NET Core, кэш Redis или базу данных или даже хранилище BLOB-объектов Azure. В приведенном выше фрагменте кода это объект `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` вызова метода, который привязывает службу кэша. Подробные сведения о том, что здесь происходит, выходит за рамки этого сценария, но ссылки приведены ниже.

> [!IMPORTANT]
> Очень важно понимать, что для веб-приложений и веб-API должен существовать один кэш маркеров на пользователя (на учетную запись). Сериализацию кэша маркеров также следует выполнять отдельно для каждой учетной записи.

Примеры использования кэшей маркеров для веб-приложений и веб-API см. в [руководстве по ASP.NET Core веб-приложения](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) в [кэше маркеров](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)на этапе 2-2. Пример реализации можно изучить в папке [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) в библиотеке [microsoft-authentication-extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) (доступна из папки [ Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web)).

## <a name="next-steps"></a>Следующие шаги

На этом этапе, когда пользователь входит в маркер, он хранится в кэше маркеров. Давайте посмотрим, как он используется в других частях веб-приложения.

> [!div class="nextstepaction"]
> [Вход в веб-приложение](scenario-web-app-call-api-sign-in.md)
