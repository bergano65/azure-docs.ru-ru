---
title: Получение маркера в веб-приложении, вызывающем веб-API — платформа Microsoft Identity | Службы
description: Узнайте, как получить маркер для веб-приложения, которое вызывает веб-API.
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
ms.openlocfilehash: 40e788099a159e1f60c0af02deccd7e3bef82744
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82181738"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Веб-приложение, вызывающее веб-API: получение маркера для приложения

Вы создали объект клиентского приложения. Теперь вы будете использовать его для получения маркера для вызова веб-API. В ASP.NET или ASP.NET Core вызов веб-API выполняется в контроллере:

- Получите маркер для веб-API с помощью кэша маркеров. Чтобы получить этот маркер, вызовите метод MSAL `AcquireTokenSilent` (или эквивалент в Microsoft. Identity. Web).
- Вызовите защищенный API, передав ему маркер доступа в качестве параметра.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Методы контроллера защищены `[Authorize]` атрибутом, который заставляет пользователей проходить проверку подлинности для использования веб-приложения. Ниже приведен код, который вызывает Microsoft Graph:

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

Эта `ITokenAcquisition` Служба внедряется функцией ASP.NET с помощью внедрения зависимостей.

Ниже приведен упрощенный код для действия `HomeController` , которое получает маркер для вызова Microsoft Graph:

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Чтобы лучше понять код, необходимый для этого сценария, см. шаг 2[2-1 (вызовы веб-приложения Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) в учебнике [MS-Identity-aspnetcore-webapp-Tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Существуют и другие сложные вариации, например:

- Вызов нескольких интерфейсов API.
- Обработка добавочного согласия и условного доступа.

Эти дополнительные действия описаны в главе 3 учебника по [3-webapp-Multi-API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Код для ASP.NET похож на код, показанный для ASP.NET Core:

- Действие контроллера, защищенное атрибутом [авторизовать], извлекает идентификатор клиента и идентификатор пользователя для `ClaimsPrincipal` члена контроллера. (ASP.NET использует `HttpContext.User` .)
- После этого он создает `IConfidentialClientApplication` объект MSAL.NET.
- Наконец, он вызывает `AcquireTokenSilent` метод конфиденциального клиентского приложения.

# <a name="java"></a>[Java](#tab/java)

В примере Java код, вызывающий API, находится в методе Жетусерсфромграф в [ауспажеконтроллер. Java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Метод пытается вызвать `getAuthResultBySilentFlow` . Если пользователь должен согласиться с дополнительными областями, код обрабатывает `MsalInteractionRequiredException` объект для вызова пользователю.

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

В образце Python код, вызывающий Microsoft Graph, находится в [app. Корректировка l53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Код пытается получить маркер из кэша маркеров. После установки заголовка авторизации он вызывает веб-API. Если не удается получить маркер, пользователь снова подписывается.

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

---

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Вызов веб-API](scenario-web-app-call-api-call-api.md)
