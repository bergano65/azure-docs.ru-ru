---
title: Реализация входа в Azure AD для веб-приложения ASP.NET
titleSuffix: Microsoft identity platform
description: Реализация входа в решение ASP.NET с использованием учетной записи Майкрософт, традиционного браузерного приложения и стандарта OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ff89d3c11ca88db14d2efd772be44aef7165a8a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964741"
---
# <a name="add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Реализация входа в веб-приложение ASP.NET с использованием учетной записи Майкрософт.

В этом руководстве описано, как реализовать вход в решение ASP.NET MVC с использованием учетной записи Майкрософт, традиционного браузерного приложения и OpenID Connect.

Завершив работу, вы получите приложение, которое поддерживает вход с использованием личных учетных записей служб outlook.com, live.com и т. п. Для входа в это приложение можно также использовать рабочие и учебные учетные записи любой компании или организации, интегрированной с Azure Active Directory.

> Для работы с этим руководством требуется Microsoft Visual Studio 2019.  У вас его нет?  [Скачайте бесплатно Visual Studio 2019](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Как работает пример приложения, созданный в этом руководстве

![Схема работы примера приложения, создаваемого в этом кратком руководстве](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

Создаваемый пример приложения основан на сценарии, когда вы с помощью браузера получаете доступ к веб-сайту ASP.NET, который предлагает пользователю пройти аутентификацию с помощью кнопки для входа. В этом сценарии большая часть работы по отображению веб-страницы происходит на стороне сервера.

## <a name="libraries"></a>Библиотеки

В этом руководстве используются следующие библиотеки:

|Библиотека|ОПИСАНИЕ|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Промежуточный слой, который позволяет приложению использовать OpenIDConnect для проверки подлинности.|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|ПО промежуточного слоя, которое позволяет приложению поддерживать пользовательский сеанс с помощью файлов cookie.|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|ПО промежуточного слоя, которое позволяет приложениям на основе OWIN работать на платформе IIS с использованием конвейера запросов ASP.NET.|

## <a name="set-up-your-project"></a>Настройка проекта

В этом разделе описано, как установить и настроить конвейер для аутентификации с использованием OpenID Connect и ПО промежуточного слоя OWIN в проекте ASP.NET.

> Предпочитаете скачать этот пример проекта Visual Studio? [Скачайте проект](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) и сразу перейдите к [регистрации приложения](#register-your-application), чтобы настроить пример кода перед выполнением.

### <a name="create-your-aspnet-project"></a>Создание проекта ASP.NET

1. В Visual Studio: Выберите **Файл** > **Создать** > **Проект**.
2. В **Visual C#\Web** выберите **Веб-приложение ASP.NET (.NET Framework)** .
3. Присвойте имя приложению и нажмите кнопку **ОК**.
4. Выберите **Пустой** и установите флажок, чтобы добавить ссылки **MVC**.

## <a name="add-authentication-components"></a>Добавление компонентов проверки подлинности

1. В Visual Studio: Выберите **Инструменты** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.
2. Добавьте *пакеты NuGet для промежуточного слоя OWIN*, введя следующие команды в окне консоли диспетчера пакетов:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>О библиотеках
> Эти библиотеки позволяют выполнять единый вход с использованием OpenID Connect и аутентификации на основе файлов cookie. После завершения проверки подлинности и отправки приложению маркера, представляющего пользователя, промежуточный слой OWIN создаст файл cookie сеанса. Затем браузер будет использовать этот файл cookie при последующих запросах, чтобы пользователю не нужно было повторно вводить пароль и проходить дополнительную проверку.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Настройка конвейера для проверки подлинности

Приведенные ниже шаги позволяют создать класс Startup в ПО промежуточного слоя OWIN для настройки аутентификации OpenID Connect. Этот класс выполняется автоматически при запуске процесса IIS.

> [!TIP]
> Если проект не содержит файл `Startup.cs` в корневой папке, сделайте следующее:
> 1. Щелкните корневую папку проекта правой кнопкой мыши и выберите **Добавить** > **Новый элемент** > **Класс Startup OWIN**.<br/>
> 2. Присвойте имя **Startup.cs**.
>
>> Убедитесь, что вы выбрали класс Startup OWIN, а не стандартный класс C#. Для этого проверьте наличие строки [assembly: OwinStartup(typeof({NameSpace}.Startup))] над пространством имен.

1. Добавьте в файл Startup.cs ссылки *OWIN* и *Microsoft.IdentityModel*:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Замените класс Startup следующим кодом.

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Azure Active Directory v2.0 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect 
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> Параметр `ValidateIssuer = false` приводится в этом кратком руководстве для упрощения. В реальных приложениях нужно проверять издателя.
> См. примеры кода, чтобы узнать, как это сделать.

<!--start-collapse-->
> ### <a name="more-information"></a>Дополнительные сведения
> Параметры, указанные в *OpenIDConnectAuthenticationOptions*, будут служить координатами приложения для взаимодействия с Azure AD. Так как ПО промежуточного слоя OpenID Connect использует файлы cookie в фоновом режиме, вам также нужно настроить аутентификацию с использованием этих файлов, как показано в коде выше. Значение *ValidateIssuer* сообщает OpenIdConnect, что доступ не ограничен одной конкретной организацией.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Добавление контроллера для обработки запросов на вход и выход

Чтобы создать контроллер, который предоставляет методы входа и выхода, сделайте следующее:

1.  Щелкните правой кнопкой мыши папку **Controllers** и выберите **Добавить** > **Контроллер**.
2.  Выберите **MVC (.NET version) Controller – Empty** (Контроллер MVC (.NET) — пусто).
3.  Выберите **Добавить**.
4.  Присвойте имя **HomeController** и щелкните **Добавить**.
5.  Добавьте в этот класс следующие ссылки OWIN:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Добавьте следующие два метода, которые будут обрабатывать операции входа и выхода для контроллера, инициируя аутентификацию:

    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }
    
    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-for-user-sign-in"></a>Создание страницы приветствия в приложении для входа пользователя

Создайте в Visual Studio представление с кнопкой входа, которое будет отображать сведения о пользователе после аутентификации:

1.  Щелкните правой кнопкой мыши папку **Views\Home** и выберите **Добавление представления**.
2.  Присвойте новому представлению имя **Index**.
3.  Добавьте следующий код HTML, включающий кнопку входа, в файл:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Дополнительные сведения
> С помощью этой страницы можно добавить кнопку входа в формате SVG с черным фоном:<br/>![Войдите с помощью учетной записи Майкрософт](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> См. расширенный список кнопок входа в [рекомендациях по использованию фирменной символики](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "BРекомендации по использованию фирменной символики).
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Добавление контроллера для отображения утверждений пользователя
Этот контроллер демонстрирует использование атрибута `[Authorize]` для защиты. Этот атрибут разрешает доступ к контроллеру только тем пользователям, которые прошли аутентификацию. В приведенном ниже коде этот атрибут используется для отображения утверждений пользователя, полученных при выполнении входа:

1.  Щелкните правой кнопкой мыши папку **Controllers** и выберите **Добавить** > **Контроллер**.
2.  Выберите **MVC {version} Controller – Empty** (Контроллер MVC {версия} — пусто).
3.  Выберите **Добавить**.
4.  Назовите его **ClaimsController**.
5.  Замените код в классе контроллера следующим кодом. Это действие добавляет в класс атрибут `[Authorize]`:

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;
    
            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;
    
            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;
    
            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;
    
            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;
    
            return View();
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Дополнительные сведения
> Атрибут `[Authorize]` означает, что все методы контроллера можно выполнять, только если пользователь прошел аутентификацию. Если пользователь, не прошедший аутентификацию, пытается получить доступ к контроллеру, OWIN инициирует запрос на аутентификацию для такого пользователя. Приведенный выше код проверяет список утверждений на наличие определенных атрибутов пользователя, включенных в маркер идентификации пользователя. Эти атрибуты включают имя и фамилию пользователя, а также субъект глобального идентификатора пользователя. Этот субъект содержит *идентификатор клиента*, который представляет идентификатор организации пользователя. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Создание представления для отображения утверждений пользователя

В Visual Studio создайте новое представление для отображения утверждений пользователя на веб-странице:

1.  Щелкните правой кнопкой мыши папку **Views\Claims** и выберите **Добавить представление**.
2.  Присвойте новому представлению имя **Index**.
3.  Добавьте в файл следующий код HTML:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```

## <a name="register-your-application"></a>Регистрация приложения

Зарегистрировать приложение и добавить сведения о его регистрации в решение можно двумя способами:

### <a name="option-1-express-mode"></a>Вариант 1. Экспресс-режим

Чтобы быстро зарегистрировать приложение, сделайте следующее:

1. Откройте на [портале Azure новую панель регистрации приложений](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs).
1. Введите имя приложения и нажмите кнопку **Зарегистрировать**.
1. Следуйте инструкциям, чтобы одним щелчком мыши скачать и автоматически настроить новое приложение.

### <a name="option-2-advanced-mode"></a>Вариант 2. Расширенный режим

Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение вручную, сделайте следующее:

1. Откройте Visual Studio и сделайте следующее:
   1. В обозревателе решений выберите проект и просмотрите окно свойств (если это окно не отображается, нажмите клавишу F4).
   1. Для параметра "SSL включен" измените значение на `True`.
   1. Щелкните правой кнопкой мыши проект в Visual Studio, выберите **Свойства** и щелкните вкладку **Интернет**. В разделе **Серверы** измените **URL-адрес проекта** на **URL-адрес SSL**.
   1. Скопируйте URL-адрес SSL. На следующем шаге вы добавите этот URL-адрес в список URL-адресов перенаправления на портале регистрации.<br/><br/>![Свойства проекта](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Войдите на [портал Azure](https://portal.azure.com) с личной учетной записью Майкрософт либо рабочей или учебной учетной записью.
1. Если ваша учетная запись предоставляет доступ к нескольким клиентам, вверху справа щелкните ее и выберите для текущего сеанса работы нужный клиент Azure AD.
1. Перейдите на страницу [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) платформы удостоверений Майкрософт для разработчиков.
1. Выберите **Новая регистрация**.
1. После появления страницы **Регистрация приложения** введите сведения о регистрации приложения:
   1. В разделе **Имя** введите понятное имя приложения, которое будет отображаться пользователям приложения, например **ASPNET-Tutorial**.
   1. Добавьте URL-адрес SSL, скопированный из Visual Studio на шаге 1 (например, `https://localhost:44368/`), в поле **URL-адрес ответа** и щелкните **Зарегистрировать**.
1. Откройте меню **Проверка подлинности**, выберите вариант **Токен идентификатора** в разделе **Неявное предоставление** и щелкните **Сохранить**.
1. Добавьте следующий код в файл web.config, который расположен в корневой папке в разделе `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Замените `ClientId` зарегистрированным идентификатором приложения.
1. Замените `redirectUri` URL-адресом SSL проекта.

## <a name="test-your-code"></a>Тестирование кода

Чтобы протестировать приложение в Visual Studio, нажмите клавишу F5 для запуска проекта. В браузере откроется адрес http://<span></span>localhost:{port} и отобразится кнопка **Вход с помощью учетной записи Майкрософт**. Нажмите кнопку, чтобы начать процесс входа в систему.

Когда вы будете готовы начать тестирование, войдите в учетную запись Azure AD (рабочую или учебную) или личную учетную запись Майкрософт (<span>live.com</span> или <span>outlook.</span>com).

![Войдите с помощью учетной записи Майкрософт](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Войдите в учетную запись Майкрософт](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

<!--start-collapse-->
> ###  <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Разрешения и согласие для конечной точки платформы удостоверений Майкрософт
>  Приложения, которые интегрируются с платформой удостоверений Майкрософт, следуют модели авторизации, при которой пользователи и администраторы контролируют доступ к данным. Когда пользователь выполнит аутентификацию в Azure AD для доступа к этому приложению, ему будет предложено подтвердить запрошенные приложением разрешения ("Просмотр базового профиля" и "Ведение доступа к данным, к которым вам был предоставлен доступ"). Подтвердив разрешения, пользователь может продолжать работу с результатами приложения. Но вместо этого пользователь может увидеть страницу **Требуется согласие администратора**, если выполняется любое из следующих условий:
>  > - Разработчик приложения добавил дополнительные разрешения, для которых требуется **согласие администратора**.
>  > - Клиент настроен (в разделе **Корпоративные приложения -> Параметры пользователя**) так, что пользователи не могут разрешать приложениям доступ к корпоративным данным от своего имени.
>
> См. сведения о [разрешениях и согласии для конечной точки платформы удостоверений Майкрософт](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent).
<!--end-collapse-->

#### <a name="view-application-results"></a>Просмотр результатов приложения

После входа пользователь перенаправляется на домашнюю страницу веб-сайта. Домашняя страница — это URL-адрес HTTPS, указанный в информации о регистрации приложения на портале регистрации приложений Майкрософт. Домашняя страница содержит приветственное сообщение *"Привет \<пользователь>",* ссылку для выхода из системы и ссылку для просмотра утверждений пользователя. При переходе по ссылке на утверждения пользователя отображается созданный ранее контроллер утверждений.

### <a name="view-the-users-claims"></a>Просмотр утверждений пользователя

Для просмотра утверждений пользователей щелкните эту ссылку. Вы увидите представление контроллера, которое доступно только пользователям, выполнившим аутентификацию.

#### <a name="view-the-claims-results"></a>Просмотр результатов утверждений

После перехода к представлению контроллера отобразится таблица, содержащая основные свойства пользователя:

|Свойство |Значение |ОПИСАНИЕ |
|---|---|---|
|**Имя** |Полное имя пользователя | Имя и фамилия пользователя
|**Имя пользователя** |user<span>@domain.com</span> | Имя пользователя, которое используется для идентификации пользователя.|
|**Тема** |Субъект |Строка, уникально идентифицирующая пользователя в Интернете.|
|**Идентификатор клиента** |Guid | **Уникальный идентификатор**, который представляет организацию Azure AD пользователя.|

Кроме того, вы увидите таблицу со всеми утверждениями, которые включены в запрос на аутентификацию. См. сведения в [справочнике по токенам Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Проверка доступа к методу, включающему атрибут Authorize (необязательно)

Для проверки доступа анонимного пользователя к контроллеру, защищенному с помощью атрибута `Authorize`, сделайте следующее:

1. Выполните выход, щелкнув соответствующую ссылку.
2. В окне браузера введите адрес http://<span></span>localhost:{port}/claims, чтобы получить доступ к контроллеру, защищенному атрибутом `Authorize`.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Ожидаемые результаты после получения доступа к защищенному контроллеру

Вам будет предложено пройти проверку подлинности, чтобы использовать представление защищенного контроллера.

## <a name="advanced-options"></a>Расширенные параметры

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Защита всего веб-сайта
Для защиты всего веб-сайта в файле **Global.asax** добавьте атрибут `AuthorizeAttribute` к фильтру `GlobalFilters` в методе `Application_Start`.

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Ограничение доступа на вход в приложение

По умолчанию после созданное при работе с этим руководством приложение будет принимать запросы на вход с личными учетными записями (включая учетные записи outlook.com, live.com и пр.), а также рабочими либо учебными учетными записями любой компании или организации, которая использует Azure AD. Этот вариант рекомендуется для приложений SaaS.

Ограничить возможность входа пользователей в приложение можно несколькими способами.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Вариант 1. Разрешение входа в приложение только пользователям отдельного экземпляра Active Directory организации (приложение с одним клиентом)

Это распространенный сценарий для *бизнес-приложений*. Если требуется, чтобы приложение принимало операции входа только с использованием учетных записей, относящихся к определенному экземпляру Azure AD (включая *гостевые учетные записи* этого экземпляра), сделайте следующее:

1. В файле web.config для значения параметра `Tenant` вместо `Common` укажите имя клиента организации (например, `contoso.onmicrosoft.com`).
2. В классе [OWIN Startup](#configure-the-authentication-pipeline) задайте аргументу `ValidateIssuer` значение `true`.

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Вариант 2. Ограничение доступа для пользователей из определенного списка организаций

Возможность входа в приложение можно предоставлять только учетным записями пользователей из организаций Azure AD, входящих в список разрешенных организаций:
1. В классе [OWIN Startup](#configure-the-authentication-pipeline) задайте аргументу `ValidateIssuer` значение `true`.
2. Установите список разрешенных организаций в качестве значения параметра `ValidIssuers`.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Вариант 3. Используйте пользовательский метод для проверки издателей

Пользовательский метод можно реализовать для проверки издателей с помощью параметра **IssuerValidator**. См. сведения о [классе TokenValidationParameters](/previous-versions/visualstudio/dn464192(v=vs.114)).

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о том, как веб-приложения вызывают веб-API.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart-guide"></a>Узнайте, как создавать приложения, используемые в этом кратком руководстве.

Узнайте, как веб-приложения вызывают веб-API платформы идентификации Майкрософт:

> [!div class="nextstepaction"]
> [Веб-приложения, которые вызывают веб-API](scenario-web-app-sign-user-overview.md)

Узнайте, как создавать веб-приложения, вызывающие Microsoft Graph:

> [!div class="nextstepaction"]
> [Руководство по Microsoft Graph для ASP.NET](https://docs.microsoft.com/graph/tutorials/aspnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Помогите нам улучшить платформу Microsoft Identity. Поделитесь своим мнением, ответив на два вопроса:

> [!div class="nextstepaction"]
> [Опрос по платформе удостоверений Майкрософт](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
