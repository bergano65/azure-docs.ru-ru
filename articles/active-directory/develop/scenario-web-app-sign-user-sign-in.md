---
title: Веб-приложение, которое входит в систему пользователей (вход) — платформа Microsoft Identity
description: Узнайте, как создать веб-приложение, которое входит в систему пользователей (вход)
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
ms.openlocfilehash: be7801515355452306cd5e7afa709a0681c7c314
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562195"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Веб-приложение, которое входит в систему пользователей — вход

Узнайте, как добавить вход в код для веб-приложения, которое входит в систему пользователей.

## <a name="sign-in"></a>Вход

Код, который мы находился в предыдущей [конфигурации кода приложения](scenario-web-app-sign-user-app-configuration.md) , — это все, что необходимо для реализации выхода. После входа пользователя в приложение вы, вероятно, захотите включить его для выхода из него. ASP.NET Core обрабатывает выход.

## <a name="what-sign-out-involves"></a>Что происходит при выходе

Выход из веб-приложения — это больше, чем удаление сведений о учетной записи, выполнившего вход, из состояния веб-приложения.
Веб-приложение также должно перенаправить пользователя на конечную точку `logout` платформы идентификации Майкрософт для выхода. Когда веб-приложение перенаправляет пользователя на `logout` конечную точку, эта конечная точка удаляет сеанс пользователя из браузера. Если ваше приложение не пойдет в `logout` конечную точку, пользователь повторно получит доступ к вашему приложению, не вводя учетные данные снова, так как у них будет действительный сеанс единого входа с конечной точкой платформы Microsoft Identity.

Дополнительные сведения см. в разделе [Отправка запроса на](v2-protocols-oidc.md#send-a-sign-out-request) выход на [платформе Microsoft Identity и](v2-protocols-oidc.md) в основной документации по протоколу OpenID Connect Connect.

## <a name="application-registration"></a>Регистрация приложения

Во время регистрации приложения вы зарегистрировали **URI завершающего выхода**. В нашем руководстве вы зарегистрировались `https://localhost:44321/signout-oidc` в поле " **URL-адрес выхода** " в разделе " **Дополнительные параметры** " на странице " **Проверка** подлинности". Дополнительные сведения см. [в разделе Регистрация приложения webApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp) .

## <a name="aspnet-core-code"></a>Код ASP.NET Core

### <a name="signout-button"></a>Кнопка выхода

Кнопка выйти отображается в `Views\Shared\_LoginPartial.cshtml` и отображается только при наличии учетной записи, прошедшей проверку подлинности (то есть когда пользователь ранее вошел в систему).

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()`действия`AccountController`

Нажатие кнопки **выйти** в веб-приложении запускает `SignOut` действие на `Account` контроллере. В предыдущих версиях основных шаблонов `Account` ASP.NET контроллер был внедрен в веб-приложение, но это не так, как теперь входит в саму платформу ASP.NET Core. 

Код для `AccountController` доступен в репозитории ASP.NET Core по адресу [AccountController.CS](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Контроль учетных записей:

- Задает универсальный код ресурса (URI `/Account/SignedOut` ) перенаправления OpenID Connect, чтобы контроллер вызывался обратно, когда Azure AD выполнит выход.
- Вызывает `Signout()`метод, который позволяет по промежуточного слоя OpenIdConnect обращаться `logout` к конечной точке платформы идентификации Майкрософт, которая:

  - Удаляет файл cookie сеанса из браузера и
  - Метод finally вызывает обратный **URL-адрес выхода**, который по умолчанию отображает подписанный просмотр страницы представления [. HTML](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) также предоставляется в составе ASP.NET Core.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Перехват вызова к `logout` конечной точке

По промежуточного слоя ASP.NET Core OpenIdConnect позволяет приложению перехватывать вызовы конечной точки платформы `logout` Microsoft Identity, предоставляя событие OpenIdConnect с именем. `OnRedirectToIdentityProviderForSignOut` Веб-приложение использует его, чтобы попытаться избежать отображения диалогового окна Выбор учетной записи для пользователя при выходе из программы. Эта перехват выполняется в `AddAzureAdV2Authentication` `Microsoft.Identity.Web` повторно используемой библиотеке. См. раздел [StartupHelpers.CS L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

```CSharp
public static IServiceCollection AddAzureAdV2Authentication(this IServiceCollection services,
                                                            IConfiguration configuration)
{
    ...
    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
        ...
        options.Authority = options.Authority + "/v2.0/";
        ...
        // Attempt to avoid displaying the select account dialog when signing out
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            var user = context.HttpContext.User;
            context.ProtocolMessage.LoginHint = user.GetLoginHint();
            context.ProtocolMessage.DomainHint = user.GetDomainHint();
            await Task.FromResult(0);
        };
    }
}
```

## <a name="aspnet-code"></a>Код ASP.NET

В ASP.NET выход запускается из метода Signing () на контроллере (для экземпляра AccountController). Этот метод не является частью ASP.NET Framework (в отличие от того, что происходит в ASP.NET Core) и не использует async, и вот почему:

- Отправка запроса на выход из OpenID Connect
- Очищает кэш
- перенаправляет на страницу, которую он хочет

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 MsalAppBuilder.ClearUserTokenCache();
 Response.Redirect("/");
}
```

## <a name="protocol"></a>Протокол

Если вы не хотите использовать ASP.NET Core или ASP.NET, ознакомьтесь с документацией по протоколам, которая доступна по адресу [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-web-app-sign-user-production.md)
