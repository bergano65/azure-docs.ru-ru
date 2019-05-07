---
title: Веб-приложение, которое поддерживает вход пользователей (вход в систему) — платформе Microsoft identity
description: Сведения о создании веб-приложения, где пользователи входа в систему (вход)
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
ms.openlocfilehash: 3fb7fbba7ec48da580d2a630ae51aa20b3307848
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074625"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Веб-приложение, которое поддерживает вход пользователей — вход

Узнайте, как добавление функции входа в код веб-приложения, где пользователи, впервые входящий в систему.

## <a name="sign-in"></a>Вход

Код, мы включили в предыдущей статье [конфигурации кода приложения](scenario-web-app-sign-user-app-configuration.md) — это все, необходимо реализовать выхода. Когда пользователь выполнил вход в приложение, может потребоваться включить их выйти из системы. ASP.NET core обрабатывает выход.

## <a name="what-sign-out-involves"></a>Включает в себя новые выйдите из системы

При выходе из веб-приложения — о более чем удаление сведений об учетной записи вошедшего в систему из состояния веб приложения.
Веб-приложения также необходимо перенаправить пользователя к версии 2.0 платформы удостоверений Microsoft `logout` конечной точки, чтобы выйти из системы. Когда веб-приложение перенаправляет пользователя для `logout` конечной точки, эта конечная точка очистит сеанс пользователя из браузера. Если приложение не переходите к `logout` конечной точки, пользователь может выполнить аутентификацию в приложении без ввода учетных данных, из-за наличия действительный единого входа в сеанс с конечной точкой версии 2.0 платформы Microsoft Identity.

Дополнительные сведения см. в разделе [Отправка запроса на выход](v2-protocols-oidc.md#send-a-sign-out-request) статьи [версии 2.0 платформы Microsoft Identity и протокол OpenID Connect](v2-protocols-oidc.md) основной документации.

## <a name="application-registration"></a>Регистрация приложения

Во время регистрации приложения, вы будете зарегистрированы **блога универсальный код Ресурса выхода**. В этом руководстве вы зарегистрировали `https://localhost:44321/signout-oidc` в **URL-адрес выхода** поле **Дополнительные параметры** статьи **проверки подлинности** страницы. Дополнительные сведения см., [ Регистрация приложения веб-приложения](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>Код ASP.NET Core

### <a name="signout-button"></a>Кнопка выхода

Кнопку выхода предоставляется в `Views\Shared\_LoginPartial.cshtml` и отображается только при отсутствии прошедшего проверку подлинности учетной записи (то есть, когда пользователь уже вошел в).

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

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()` действие `AccountController`

Нажав клавишу **выйдите из системы** кнопку на триггеры приложения web `SignOut` действие на `Account` контроллера. В предыдущих версиях ASP.NET основные шаблоны `Account` контроллера внедрен в веб-приложении, но это не так, как он теперь является частью сама платформа ASP.NET Core. 

Код для `AccountController` доступен из репозитория ASP.NET core в из [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Контроль учетных записей:

- URI для перенаправления устанавливает OpenID `/Account/SignedOut` , чтобы контроллер вызывается обратно в том случае, когда Azure AD выполнил выхода
- Вызовы `Signout()`, благодаря которому по промежуточного слоя OpenIdConnect обратитесь к платформе Microsoft identity `logout` конечная точка которой:

  - Удаляет файл cookie сеанса из браузера, и
  - Наконец, вызовы выполняет обратный **URL-адрес выхода**, который) по умолчанию отображается со знаком представление страницы [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) также предоставляется как часть ASP.NET Core.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Перехват вызова `logout` конечной точки

По промежуточного слоя ASP.NET Core OpenIdConnect позволяет приложению для перехвата вызовов к платформе Microsoft identity `logout` конечной точки, предоставляя OpenIdConnect событие с именем `OnRedirectToIdentityProviderForSignOut`. Веб-приложение использует его для стараюсь избегать диалогового окна выберите учетную запись, чтобы отображаться для пользователя, когда выполняется выход. Выполняется перехвата `AddAzureAdV2Authentication` из `Microsoft.Identity.Web` многократно используемой библиотеки. См. в разделе [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

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

В ASP.NET выполняется выход из метода SignOut() на контроллере (например AccountController) активируется. Этот метод не является частью платформы ASP.NET (в отличие от что происходит в ASP.NET Core) и не использует async, и именно его:

- отправляет запрос выхода OpenId
- Очищает кэш
- перенаправляет на страницу, на который он намерен

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

Если вы не хотите использовать ASP.NET Core или ASP.NET, можно взглянуть на документации по протоколу, который доступен из [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-web-app-sign-user-production.md)
