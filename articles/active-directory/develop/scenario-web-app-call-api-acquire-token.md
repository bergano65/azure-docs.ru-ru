---
title: Получить токен в веб-приложении, которое вызывает веб-AI - платформа идентификации Microsoft (ru) Azure
description: Узнайте, как приобрести токен для веб-приложения, которое вызывает web-аБО
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
ms.openlocfilehash: 1069b4288f8253ccb9a7774b3144d10d85dcdd36
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537123"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Веб-приложение, которое вызывает веб-AIS: Приобрести маркер для приложения

Вы создали объект клиентского приложения. Теперь вы будете использовать его для приобретения маркера для вызова веб-API. В ASP.NET или ASP.NET Core вызов веб-API выполняется в контроллере:

- Получите маркер для web-API, используя кэш маркеров. Чтобы получить этот маркер, `AcquireTokenSilent` вы называете метод.
- Вызовите защищенный API, передав токен доступа к нему в качестве параметра.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Методы контроллера защищены `[Authorize]` атрибутом, который заставляет пользователей быть аутентифицированными для использования веб-приложения. Вот код, который называется Microsoft Graph:

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

Услуга `ITokenAcquisition` вводится ASP.NET с помощью инъекций зависимости.

Вот упрощенный код для действий `HomeController`, который получает маркер для вызова Microsoft Graph:

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Чтобы лучше понять код, необходимый для этого сценария, см фазы 2 ([2-1-Web приложение вызывает Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) шаг [мс-идентичность-aspnetcore-webapp-учебник.](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

Существуют и другие сложные варианты, такие как:

- Вызов нескольких AIS.
- Обработка инкрементного согласия и условного доступа.

Эти передовые шаги описаны в главе [3-WebApp-мульти-AIS](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) учебник.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Код для ASP.NET похож на код, показанный для ASP.NET Core:

- Действие контроллера, защищенное атрибутом «Авторизовать», извлекает идентификатор клиента и идентификатор пользователя `ClaimsPrincipal` пользователя пользователя контроллера. (ASP.NET `HttpContext.User`использует .)
- Оттуда он строит MSAL.NET `IConfidentialClientApplication` объект.
- Наконец, он `AcquireTokenSilent` называет метод конфиденциального приложения клиента.

# <a name="java"></a>[Java](#tab/java)

В примере Java код, который вызывает API, находится в методе getUsersFromGraph в [AuthPageController.java-L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Метод пытается вызвать `getAuthResultBySilentFlow`. Если пользователю необходимо дать согласие на большее `MsalInteractionRequiredException` количеством областей, код обрабатывает объект, чтобы бросить вызов пользователю.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="python"></a>[Python](#tab/python)

В образце Python код, который вызывает Microsoft Graph, находится в [app.py'L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Код пытается получить токен из кэша маркеров. Затем, после установки заголовка авторизации, он вызывает web API. Если он не может получить маркер, он снова подписывает пользователя.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Вызов веб-API](scenario-web-app-call-api-call-api.md)
