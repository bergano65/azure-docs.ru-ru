---
title: Напишите веб-приложение, которое вписывается/выходите на пользователях - платформа идентификации Microsoft Azure
description: Узнайте, как создать веб-приложение, которое вписывается/выходить из пользователей
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
ms.openlocfilehash: cbec6c5644690f6d7e522294a37c8ea5e2d49e30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701524"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Веб-приложение, которое вписывается в пользователей: Восподписание и выход из системы

Узнайте, как добавить в свой код для веб-приложения, которое подписывается в пользователях. Затем узнайте, как позволить им выйти.

## <a name="sign-in"></a>Вход

Регистрация состоит из двух частей:

- Кнопка входной системы на странице HTML
- Действие входа в код-за в контроллере

### <a name="sign-in-button"></a>Кнопка вхвора

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

В ASP.NET Core кнопка вхастыва заключается в. `Views\Shared\_LoginPartial.cshtml` Он отображается только в том случае, если нет подлинной учетной записи. То есть, он отображается, когда пользователь еще не зарегистрировался или не зарегистрировался.

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

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

В ASP.NET MVC, кнопка выхода `Views\Shared\_LoginPartial.cshtml`выставлена в . Он отображается только при наличии подлинной учетной записи. То есть, он отображается, когда пользователь ранее вписался.

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

# <a name="java"></a>[Java](#tab/java)

В нашем Java quickstart кнопка вхильярной находится в [файле main/resources/templates/index.html.](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html)

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

# <a name="python"></a>[Python](#tab/python)

В quickstart Python нет кнопки вхастывки. Закодирование автоматически подсказывает пользователю для входиный, когда он достигает корня веб-приложения. Смотрите [приложение.py'L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn`действия контроллера

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

В ASP.NET, выбор кнопки **входа в** веб-приложение `SignIn` запускает `AccountController` действие на контроллере. В предыдущих версиях ASP.NET основных шаблонов `Account` контроллер был встроен в веб-приложение. Это уже не так, потому что контроллер теперь является частью ASP.NET Core.

Код для `AccountController` доступной в репозитории ASP.NET Core [в AccountController.cs.](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs) Управление учетной записью бросает вызов пользователю, перенаправив его на конечную точку платформы Майкрософт. Для получения подробно [ASP.NETй](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) информации см.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

В ASP.NET, выйти из `SignOut()` него срабатывает метод на контроллере (например, [AccountController.cs-L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Этот метод не является частью ASP.NET структуры (вопреки тому, что происходит в ASP.NET Core). Он посылает OpenID войти в вызов после предложения перенаправить URI.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

В Java, выход обрабатывается путем вызова `logout` платформы идентификации `post_logout_redirect_uri` Microsoft конечную точку непосредственно и предоставление значения. Для получения более [подробной](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)информации см.

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

# <a name="python"></a>[Python](#tab/python)

В отличие от других платформ, MSAL Python заботится о том, чтобы позволить пользователю войти со страницы входа. Смотрите [app.py-L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

Метод `_build_msal_app()` определен в [app.py'L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) следующим образом:

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
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

После того, как пользователь впишется в ваше приложение, вы хотите, чтобы позволить ему выйти.

## <a name="sign-out"></a>Функция выхода

Выход из веб-приложения включает в себя нечто большее, чем удаление информации о вписавшем аккаунте из состояния веб-приложения.
Веб-приложение также должно перенаправить пользователя на `logout` конечную точку платформы Майкрософт, чтобы выйти из системы. 

Когда веб-приложение перенаправляет пользователя `logout` в конечную точку, эта конечная точка очищает сеанс пользователя от браузера. Если приложение не перешел `logout` в конечную точку, пользователь повторно подключит подлинность к вашему приложению, не введя свои учетные данные снова. Причина в том, что они будут иметь действительный один сеанс ввода с конечным пунктом платформы идентификации Майкрософт.

Чтобы узнать больше, смотрите раздел [«Отправить запрос на вывешение»](v2-protocols-oidc.md#send-a-sign-out-request) в [платформе идентификации Майкрософт и документации протокола OpenID Connect.](v2-protocols-oidc.md)

### <a name="application-registration"></a>Регистрация приложения

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Во время регистрации заявления вы регистрируете пост-логит URI. В нашем учебнике `https://localhost:44321/signout-oidc` вы зарегистрировались в поле **URL-адреса Logout** раздела **Расширенные настройки** на странице **аутентификации.** Для получения подробной информации, [см.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Во время регистрации заявления вы регистрируете пост-логит URI. В нашем учебнике `https://localhost:44308/Account/EndSession` вы зарегистрировались в поле **URL-адреса Logout** раздела **Расширенные настройки** на странице **аутентификации.** Для получения подробной информации, [см.](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)

# <a name="java"></a>[Java](#tab/java)

Во время регистрации заявления вы регистрируете пост-логит URI. В нашем учебнике `http://localhost:8080/msal4jsample/sign_out` вы зарегистрировались в поле **URL-адреса Logout** раздела **Расширенные настройки** на странице **аутентификации.**

# <a name="python"></a>[Python](#tab/python)

Во время регистрации заявки не нужно регистрировать дополнительный URL-адрес входа. Приложение будет отозвано на свой основной URL.

---

### <a name="sign-out-button"></a>Кнопка выхода

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

В ASP.NET Core кнопка вывески разоблачается в `Views\Shared\_LoginPartial.cshtml`. Он отображается только при наличии подлинной учетной записи. То есть, он отображается, когда пользователь ранее вписался.

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

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

В ASP.NET MVC, кнопка выхода `Views\Shared\_LoginPartial.cshtml`выставлена в . Он отображается только при наличии подлинной учетной записи. То есть, он отображается, когда пользователь ранее вписался.

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

# <a name="java"></a>[Java](#tab/java)

В нашем Java quickstart кнопка вывески находится в файле main/resources/templates/auth_page.html.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="python"></a>[Python](#tab/python)

В quickstart Python кнопка выхода находится в [шаблонах/индексе.html-L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) файла.

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

### <a name="signout-action-of-the-controller"></a>`SignOut`действия контроллера

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

В ASP.NET, выбор кнопки **входа** в веб-приложение `SignOut` вызывает `AccountController` действие на контроллере. В предыдущих версиях шаблонов `Account` ASP.NET Core контроллер был встроен в веб-приложение. Это уже не так, потому что контроллер теперь является частью ASP.NET Core.

Код для `AccountController` этого доступен в ASP.NET основного репозитория [в AccountController.cs.](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs) Контроль учетной записи:

- Устанавливает OpenID перенаправление `/Account/SignedOut` URI на то, чтобы контроллер был отозван, когда Azure AD завершила выйти из системы.
- Вызовы, `Signout()`что позволяет Посреднику OpenID `logout` Connect связаться с конечной точкой платформы Майкрософт. Конечная точка тогда:

  - Очищает файлcookieо-файловое сеанса от браузера.
  - Перезванивает URL-адрес выхода. По умолчанию URL-адрес выхода отображает страницу с подписью [SignedOut.html.](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) Эта страница также предоставляется в рамках ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

В ASP.NET, выйти из `SignOut()` него срабатывает метод на контроллере (например, [AccountController.cs-L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Этот метод не является частью ASP.NET структуры, вопреки тому, что происходит в ASP.NET Core. Это:

- Отправляет openID вызов регистрации.
- Очищает кэш.
- Перенаправляет на страницу, которую она хочет.

```csharp
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

# <a name="java"></a>[Java](#tab/java)

В Java, выход обрабатывается путем вызова `logout` платформы идентификации `post_logout_redirect_uri` Microsoft конечную точку непосредственно и предоставление значения. Для получения более [подробной](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)информации см.

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

# <a name="python"></a>[Python](#tab/python)

Код, который выписывает пользователя, находится в [app.py-L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Перехват вызова в `logout` конечную точку

Пост-выход URI позволяет приложениям участвовать в глобальном выставке.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Программное обеспечение ASP.NET Core OpenID Connect позволяет приложению перехватывать вызов на конечную точку платформы `logout` Майкрософт, предоставляя событие OpenID Connect под названием. `OnRedirectToIdentityProviderForSignOut` Пример того, как подписаться на это событие (для очистки кэша маркеров) можно оценить [microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs-L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156).

```csharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

В ASP.NET делегируете промежуточного программное обеспечение для выполнения выхода, очистив файлcookieо-файловик сеанса:

```csharp
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

# <a name="java"></a>[Java](#tab/java)

В Java quickstart, после выхода перенаправить URI просто отображает index.html страницу.

# <a name="python"></a>[Python](#tab/python)

В quickstart Python, после выхода перенаправить URI просто отображает index.html страницу.

---

## <a name="protocol"></a>Протокол

Если вы хотите узнать больше о регистрации, прочитайте протокольную документацию, доступную из [Open ID Connect.](./v2-protocols-oidc.md)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Перенос в рабочую среду](scenario-web-app-sign-user-production.md)
