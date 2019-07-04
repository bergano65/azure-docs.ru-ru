---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8785f89a335f0ae7d983f267176da1656aee57a0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185893"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Использование библиотеки аутентификации Майкрософт (MSAL) для входа пользователя

1. Добавьте в файл `index.html` следующий код внутри тегов `<script></script>`:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "Enter_the_Application_Id_here"
            authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };

    var graphConfig = {
        graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
    };

    // this can be used for login or token request, however in more complex situations
    // this can have diverging options
    var requestObj = {
        scopes: ["user.read"]
    };

    var myMSALObj = new Msal.UserAgentApplication(msalConfig);
    // Register Callbacks for redirect flow
    myMSALObj.handleRedirectCallback(authRedirectCallBack);


    function signIn() {

        myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
            //Login Success
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }).catch(function (error) {
            console.log(error);
        });
    }

    function acquireTokenPopupAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
        });
    }


    function graphAPICallback(data) {
        document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
    }


    function showWelcomeMessage() {
        var divWelcome = document.getElementById('WelcomeMessage');
        divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
        var loginbutton = document.getElementById('SignIn');
        loginbutton.innerHTML = 'Sign Out';
        loginbutton.setAttribute('onclick', 'signOut();');
    }


    //This function can be removed if you do not need to support IE
    function acquireTokenRedirectAndCallMSGraph() {
         //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
         myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
             callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
         }).catch(function (error) {
             console.log(error);
             // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
             // Call acquireTokenRedirect
             if (requiresInteraction(error.errorCode)) {
                 myMSALObj.acquireTokenRedirect(requestObj);
             }
         });
     }


    function authRedirectCallBack(error, response) {
        if (error) {
            console.log(error);
        }
        else {
            if (response.tokenType === "access_token") {
                callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
            } else {
                console.log("token type is:" + response.tokenType);
            }
        }
    }

    function requiresInteraction(errorCode) {
        if (!errorCode || !errorCode.length) {
            return false;
        }
        return errorCode === "consent_required" ||
            errorCode === "interaction_required" ||
            errorCode === "login_required";
    }

    // Browser check variables
    var ua = window.navigator.userAgent;
    var msie = ua.indexOf('MSIE ');
    var msie11 = ua.indexOf('Trident/');
    var msedge = ua.indexOf('Edge/');
    var isIE = msie > 0 || msie11 > 0;
    var isEdge = msedge > 0;
    //If you support IE, our recommendation is that you sign-in using Redirect APIs
    //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
    // can change this to default an experience outside browser use
    var loginType = isIE ? "REDIRECT" : "POPUP";

    if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
    }
    else if (loginType === 'REDIRECT') {
        document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
        };
        if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
    } else {
        console.error('Please set a valid login type');
    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Дополнительные сведения

Когда пользователь впервые нажимает кнопку **Войти**, метод `signIn` вызывает `loginPopup` для входа пользователя в систему. В результате выполнения этого метода откроется всплывающее окно для *конечной точки платформы удостоверений Майкрософт* для запроса и проверки учетных данных пользователя. В результате успешного входа пользователь перенаправляется обратно на исходную страницу *index.html*, поступает маркер, обрабатываемый `msal.js`, после чего кэшируются сведения, содержащиеся в маркере. Этот маркер известен как *маркер идентификатора*. Он содержит основные сведения о пользователе, такие как отображаемое имя пользователя. Если вы планируете использовать какие-либо данные, предоставляемые этим маркером, то необходимо убедиться, что маркер проверен внутренним сервером. Это позволит гарантировать, что маркер был выдан допустимому пользователю для вашего приложения.

Одностраничное приложение, создаваемое в рамках этого руководства вызывает `acquireTokenSilent` и (или) `acquireTokenPopup`, чтобы получить *маркер доступа*, используемый при запросе API Microsoft Graph для получения сведений из профиля пользователя. Если вам нужен пример, проверяющий маркер идентификатора, ознакомьтесь с [этим] примером приложения на портале GitHub — (https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "active-directory-javascript-singlepageapp-dotnet-webapi-v2"). В нем для проверки маркеров используется веб-API ASP.NET.

#### <a name="getting-a-user-token-interactively"></a>Интерактивное получение маркера пользователя

После первоначального входа вам не нужно требовать от пользователей повторно проходить аутентификацию каждый раз, когда они запрашивают маркер для доступа к ресурсу, поэтому в большинстве случаев для получения маркеров следует использовать *acquireTokenSilent*. Но существуют ситуации, когда пользователи должны взаимодействовать с конечной точкой платформы удостоверений Майкрософт. Далее приведены некоторые примеры.

- Пользователям может потребоваться повторно ввести учетные данные, так как истек срок действия пароля
- Ваше приложение запрашивает доступ к ресурсу, на обращение к которому пользователь должен дать согласие
- Требуется двухфакторная проверка подлинности

Вызов *acquireTokenPopup* приводит к отображению всплывающего окна (или вызов *acquireTokenPopup* приводит к перенаправлению пользователей на конечную точку платформы удостоверений Майкрософт), где для взаимодействия пользователю нужно подтвердить свои учетные данные, дав согласие на доступ к требуемому ресурсу, или выполнить двухфакторную аутентификацию.

#### <a name="getting-a-user-token-silently"></a>Автоматическое получение маркера пользователя

Метод `acquireTokenSilent` обрабатывает получение и обновление маркера без участия пользователя. После первого выполнения метода `loginPopup` (или `loginRedirect`) обычно используется метод `acquireTokenSilent`, чтобы получить маркеры для доступа к защищенным ресурсам для последующих вызовов (например, автоматическое выполнение запросов или обновлений маркеров).
В некоторых случаях `acquireTokenSilent` может завершиться ошибкой, например если истек срок действия пароля пользователя. Приложение может обработать это исключение двумя способами:

1. Немедленно вызвать `acquireTokenPopup`, в результате чего пользователю будет предложено выполнить вход. Этот шаблон обычно используется в интерактивных приложениях, где пользователю недоступно не прошедшее проверку подлинности содержимое. Пример, созданный в ходе пошаговой настройки, использует этот шаблон.

2. Приложения также могут визуально уведомить пользователя, что требуется интерактивный вход, чтобы пользователь мог выбрать подходящее время для входа или приложение могло повторить метод `acquireTokenSilent` ​​позднее. Обычно это применимо для тех случаев, когда пользователь может использовать другие функции приложения, на которые это не влияет, например, когда в приложении есть не прошедшее проверку подлинности содержимое. В этом случае пользователь может решить, когда ему требуется вход для доступа к защищенному ресурсу, или обновить устаревшие данные.

> [!NOTE]
> В этом кратком руководстве для Internet Explorer используются методы `loginRedirect` и `acquireTokenRedirect` из-за [известной проблемы](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) с обработкой всплывающих окон в Internet Explorer.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Вызов API Microsoft Graph с помощью полученного маркера

Добавьте в файл `index.html` следующий код внутри тегов `<script></script>`:

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Дополнительные сведения о вызове REST через защищенный API

В примере приложения, созданном в этом руководстве, метод `callMSGraph()` выполняет HTTP-запрос `GET` к защищенному ресурсу, требующему маркер, а затем возвращает содержимое вызывающему объекту. Этот метод добавляет полученный маркер в *заголовок авторизации HTTP*. Для примера приложения, созданного в этом руководстве, ресурс — это конечная точка *me* API Microsoft Graph, которая отображает сведения о профиле пользователя.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Добавление метода для выхода пользователя

Добавьте в файл `index.html` следующий код внутри тегов `<script></script>`:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```
