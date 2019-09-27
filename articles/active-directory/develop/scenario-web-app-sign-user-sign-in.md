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
ms.openlocfilehash: d5c45005d6a54765458b463acb12c21a1f3b6d0c
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336768"
---
# <a name="web-app-that-signs-in-users---sign-in-and-sign-out"></a>Веб-приложение, которое входит в систему пользователей — вход и выход

Узнайте, как добавить вход в код для веб-приложения, которое выполняет вход пользователей, а затем — как разрешить выход

## <a name="sign-in"></a>Вход

Вход состоит из двух частей:

- кнопка входа на странице HTML
- действие входа в коде программной части контроллера

### <a name="sign-in-button"></a>Кнопка входа

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

В ASP.NET Core кнопка входа предоставляется в `Views\Shared\_LoginPartial.cshtml` и отображается только при отсутствии учетной записи, прошедшей проверку подлинности (то есть когда пользователь еще не вошел в систему или не выполнил выход).

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
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
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

В нашем кратком руководстве по Java кнопка выхода находится в файле [Main/Resources/Templates/index.HTML](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="pythontabpython"></a>[Python](#tab/python)

В кратком руководстве по Python отсутствует кнопка входа. При достижении корня веб-приложения пользователь автоматически запрашивает вход с помощью кода программной части. См [. раздел App. Корректировка # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18)

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="login-action-of-the-controller"></a>`Login`действие контроллера

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

В ASP.NET, нажав кнопку **входа** в веб-приложении, вы активируете `SignIn` действие на `AccountController` контроллере. В предыдущих версиях ASP.NET Core Templates `Account` контроллер был внедрен в веб-приложение, но теперь он больше не является частью самой платформы ASP.NET Core.

Код для `AccountController` доступен в репозитории ASP.NET Core из [AccountController.CS](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Управление учетными записями вызывает проблему пользователя путем перенаправления на конечную точку платформы идентификации Майкрософт. Дополнительные сведения см. в описании метода [входа](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) , который входит в состав ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

В ASP.NET выход запускается из `SignOut()` метода контроллера (например, [AccountController. CS # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Этот метод не является частью ASP.NET Framework (в отличие от того, что происходит в ASP.NET Core). Им

- отправляет запрос на вход в OpenID Connect после предложения URI перенаправления

```CSharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

В Java выход осуществляется путем прямого вызова конечной точки выхода платформы идентификации Майкрософт и предоставления post_logout_redirect_uri. Дополнительные сведения см [. в разделе ауспажеконтроллер. Java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="pythontabpython"></a>[Python](#tab/python)

В отличие от других платформ, MSAL. Python выполняет вход пользователя со страницы входа. См [. раздел App. Корректировка # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28)

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use empty list [] to just sign in,
                           # here we choose to also collect end user consent upfront
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

с помощью метода _build_msal_app (), определенного в [app. Корректировка # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) , следующим образом:

```Python
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

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all account(s) belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

После входа пользователя в приложение вы, вероятно, захотите включить его для выхода из него.

## <a name="sign-out"></a>Выйти

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

Во время регистрации приложения не нужно регистрировать дополнительный URL-адрес для выхода. Приложение будет вызываться обратно по его основному URL-адресу

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

В кратком руководстве по Python кнопка выхода находится в файле [Templates/index. HTML # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python Web App</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`Signout`действие контроллера

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

В ASP.NET нажатие кнопки **выхода** в веб-приложении запускает `SignOut` действие на `AccountController` контроллере. В предыдущих версиях ASP.NET Core Templates `Account` контроллер был внедрен в веб-приложение, но теперь он больше не является частью самой платформы ASP.NET Core.

Код для `AccountController` доступен в репозитории ASP.NET Core по адресу [AccountController.CS](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Контроль учетных записей:

- Задает универсальный код ресурса (URI `/Account/SignedOut` ) перенаправления OpenID Connect, чтобы контроллер вызывался обратно, когда Azure AD завершает выход.
- Вызывает `Signout()`метод, который позволяет по промежуточного слоя OpenIdConnect обращаться `logout` к конечной точке платформы идентификации Майкрософт, которая:

  - Удаляет файл cookie сеанса из браузера и
  - Наконец, вызывает обратный **URL-адрес выхода**, который по умолчанию отображает подписанный просмотр страницы представления [. HTML](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) также предоставляется в составе ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

В ASP.NET выход запускается из `SignOut()` метода контроллера (например, [AccountController. CS # L25-l31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Этот метод не является частью ASP.NET Framework (в отличие от того, что происходит в ASP.NET Core). Им

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

В Java выход осуществляется путем прямого вызова конечной точки выхода платформы идентификации Майкрософт и предоставления post_logout_redirect_uri. Дополнительные сведения см [. в разделе ауспажеконтроллер. Java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)

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

Код, который входит в систему пользователя, находится в [app. Корректировка # L46-l52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48)

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out user and its token cache from session
    return redirect(  # Also need to logout from Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
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

## <a name="protocol"></a>Protocol

Если вы хотите узнать больше о выходе, ознакомьтесь с документацией по протоколам, которая доступна по адресу [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-web-app-sign-user-production.md)
