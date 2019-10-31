---
title: Веб-приложение, вызывающее веб-API (получение маркера для приложения) — платформа Microsoft Identity
description: Узнайте, как создать веб-приложение, вызывающее веб-API (получение маркера для приложения).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: a259fbcf3fde84edccafbcd2fd6594ddb623edfd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175330"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Веб-приложение, вызывающее веб-API — получение маркера для приложения

Теперь, когда вы создали клиентский объект приложения, вы будете использовать его для получения маркера для вызова веб-API. В ASP.NET или ASP.NET Core вызов веб-API выполняется в контроллере. Это примерно так:

- Получение маркера для веб-API с помощью кэша маркеров. Чтобы получить этот маркер, вызовите `AcquireTokenSilent`.
- Вызов защищенного API с помощью маркера доступа.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Методы контроллера защищены атрибутом `[Authorize]`, который заставляет пользователей проходить проверку подлинности для использования веб-приложения. Ниже приведен код, который вызывает Microsoft Graph.

```CSharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions(see code below)

}
```

`ITokenAcquisition` служба внедряется с помощью ASP.NET посредством внедрения зависимостей.


Ниже приведен упрощенный код действия HomeController, который получает маркер для вызова Microsoft Graph.

```CSharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // use the access token to call a protected web API
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Чтобы лучше понять код, необходимый для этого сценария, см. шаг 2 2-1 ([вызов веб-приложения Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) в учебнике [MS-Identity-aspnetcore-webapp-Tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Существует множество дополнительных сложностей, таких как:

- Вызов нескольких API,
- Обработка добавочного согласия и условного доступа.

Эти дополнительные действия обрабатываются в главе 3 руководства [3-webapp-Multi-API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) .

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Они похожи в ASP.NET:

- Действие контроллера, защищенное атрибутом [авторизовать], извлекает идентификатор клиента и идентификатор пользователя `ClaimsPrincipal` элемента контроллера. (ASP.NET использует `HttpContext.User`.)
- После этого он создает `IConfidentialClientApplication`MSAL.NET.
- Наконец, он вызывает метод `AcquireTokenSilent` конфиденциального клиентского приложения.

Код похож на код, показанный для ASP.NET Core.

# <a name="javatabjava"></a>[Java](#tab/java)

В примере Java код, вызывающий API, находится в методе Жетусерсфромграф [ауспажеконтроллер. Java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Он пытается вызвать `getAuthResultBySilentFlow`. Если пользователь должен согласиться с дополнительными областями, код обрабатывает `MsalInteractionRequiredException`, чтобы выдать пользователю запрос.

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

            // If silent call returns MsalInteractionRequired, then redirect to Authorization endpoint
            // so user can consent to new scopes
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
// Code omitted here.
```

# <a name="pythontabpython"></a>[Python](#tab/python)

В примере Python код, вызывающий Microsoft Graph, находится в [app. Корректировка l53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Он пытается получить маркер из кэша маркеров, а затем вызывает API EB после установки заголовка авторизации. Если это не так, он повторно подписывает пользователя.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Вызов веб-API](scenario-web-app-call-api-call-api.md)
