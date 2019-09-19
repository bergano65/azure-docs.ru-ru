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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ed61a09ffc76b4813dcb97330d3a1a436aa16eb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086457"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Веб-приложение, которое входит в систему пользователей — вход

Узнайте, как добавить вход в код для веб-приложения, которое входит в систему пользователей.

## <a name="sign-in"></a>Вход

Код, который мы рассматривали в предыдущей [конфигурации](scenario-web-app-sign-user-app-configuration.md) , — это все, что необходимо для реализации входа.
После входа пользователя в приложение вы, вероятно, захотите включить его для выхода из него. ASP.NET Core обрабатывает выход.

## <a name="what-sign-out-involves"></a>Что происходит при выходе

Выход из веб-приложения — это больше, чем удаление сведений о учетной записи, выполнившего вход, из состояния веб-приложения.
Веб-приложение также должно перенаправить пользователя на конечную точку `logout` платформы идентификации Майкрософт для выхода. Когда веб-приложение перенаправляет пользователя на `logout` конечную точку, эта конечная точка удаляет сеанс пользователя из браузера. Если ваше приложение не пойдет в `logout` конечную точку, пользователь повторно получит доступ к вашему приложению, не вводя учетные данные снова, так как у них будет действительный сеанс единого входа с конечной точкой платформы Microsoft Identity.

Дополнительные сведения см. в разделе [Отправка запроса на](v2-protocols-oidc.md#send-a-sign-out-request) выход на [платформе Microsoft Identity и в основной документации по протоколу OpenID Connect Connect](v2-protocols-oidc.md) .

### <a name="application-registration"></a>Регистрация приложения

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Во время регистрации приложения вы зарегистрировали **URI завершающего выхода**. В нашем руководстве вы зарегистрировались `https://localhost:44321/signout-oidc` в поле " **URL-адрес выхода** " в разделе " **Дополнительные параметры** " на странице " **Проверка подлинности** ". Дополнительные сведения см. [в разделе Регистрация приложения webApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp) .

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Во время регистрации приложения вы зарегистрировали **URI завершающего выхода**. В нашем руководстве вы зарегистрировались `https://localhost:44308/Account/EndSession` в поле " **URL-адрес выхода** " в разделе " **Дополнительные параметры** " на странице " **Проверка подлинности** ". Дополнительные сведения см. [в разделе Регистрация приложения webApp](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet) .

# <a name="javatabjava"></a>[Java](#tab/java)

Во время регистрации приложения вы регистрируете **URI завершающего выхода**. В нашем руководстве вы зарегистрировались `http://localhost:8080/msal4jsample/` в поле " **URL-адрес выхода** " в разделе " **Дополнительные параметры** " на странице " **Проверка подлинности** ".

# <a name="pythontabpython"></a>[Python](#tab/python)

Во время регистрации приложения не нужно регистрировать URL-адрес выхода. В примере не реализован глобальный выход

---

### <a name="sign-out-button"></a>Кнопка выхода

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

В ASP.NET Core кнопка выхода предоставляется в `Views\Shared\_LoginPartial.cshtml` и отображается только при наличии учетной записи, прошедшей проверку подлинности (то есть когда пользователь ранее вошел в систему).

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

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

В ASP.NET MVC кнопка выхода предоставляется в `Views\Shared\_LoginPartial.cshtml` и отображается только при наличии учетной записи, прошедшей проверку подлинности (то есть когда пользователь ранее вошел в систему).

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

В нашем кратком руководстве по Java кнопка выхода находится в файле main/resources/templates/auth_page.html

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="pythontabpython"></a>[Python](#tab/python)

В кратком руководстве по Python кнопка выхода находится в файле [Templates/Display.HTML](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/templates/display.html#L18-L20)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Acquire Token Result </title>
</head>
<body>
    {% if cond  %}
        <p1><b>Your information</b> </p1>
        <table>
        {% for key, value in auth_result.items() %}
           <tr>
                <th> {{ key }} </th>
                <td> {{ value }} </td>
           </tr>
        {% endfor %}
        </table>
        <form action="/logout" >
            <input type="submit" value=" Logout"/>
        </form>
    {% else %}
        <p1><b> {{auth_result}} </b> </p1>
        <form action="/authenticate" >
            <input type="submit" value=" Sign-in"/>
        </form>
    {% endif %}
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`Signout()`действие контроллера

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

В ASP.NET нажатие кнопки **выхода** в веб-приложении запускает `SignOut` действие на `AccountController` контроллере. В предыдущих версиях ASP.NET Core Templates `Account` контроллер был внедрен в веб-приложение, но теперь он больше не является частью самой платформы ASP.NET Core.

Код для `AccountController` доступен в репозитории ASP.NET Core по адресу [AccountController.CS](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Контроль учетных записей:

- Задает универсальный код ресурса (URI `/Account/SignedOut` ) перенаправления OpenID Connect, чтобы контроллер вызывался обратно, когда Azure AD выполнил выход.
- Вызывает `Signout()`метод, который позволяет по промежуточного слоя OpenIdConnect обращаться `logout` к конечной точке платформы идентификации Майкрософт, которая:

  - Удаляет файл cookie сеанса из браузера и
  - Наконец, вызывает обратный **URL-адрес выхода**, который по умолчанию отображает подписанный просмотр страницы представления [. HTML](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) также предоставляется в составе ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

В ASP.NET выход запускается из `SignOut()` метода контроллера (например, AccountController). Этот метод не является частью ASP.NET Framework (в отличие от того, что происходит в ASP.NET Core). Он не использует `async`, и вот почему:

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
 Response.Redirect("/");
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

В Java выход осуществляется путем прямого вызова конечной точки выхода платформы идентификации Майкрософт и предоставления post_logout_redirect_uri.

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
@app.route("/logout")
def logout():
    return flask.redirect(flask.url_for('index'))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Перехват вызова к `logout` конечной точке

URI, выполняемый после выхода, позволяет приложениям участвовать в глобальном выходе.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

По промежуточного слоя ASP.NET Core OpenIdConnect позволяет приложению перехватывать вызовы конечной точки платформы `logout` Microsoft Identity, предоставляя событие OpenIdConnect с именем. `OnRedirectToIdentityProviderForSignOut` Пример подписки на это событие (очистка кэша маркеров) см. в разделе [Microsoft. Identity. Web/вебаппсервицеколлектионекстенсионс. CS # L151-L156.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156)

```CSharp
               // Handling the global sign-out
                options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
                {
                    // Forget about the signed-in user
                };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

В ASP.NET вы делегируем по промежуточного слоя, чтобы выполнить выход, очистив файл cookie сеанса:

```CSharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

В нашем кратком руководстве по Java в URI перенаправления после выхода отображается только страница index. HTML. 

# <a name="pythontabpython"></a>[Python](#tab/python)

В кратком руководстве по Python в URI перенаправления после выхода просто отображается страница index. HTML.

---

## <a name="protocol"></a>Протокол

Если вы хотите узнать больше о выходе, ознакомьтесь с документацией по протоколам, которая доступна по адресу [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-web-app-sign-user-production.md)
