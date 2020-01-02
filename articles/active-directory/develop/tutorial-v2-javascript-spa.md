---
title: Руководство по работе с одностраничным приложением JavaScript на платформе удостоверений Майкрософт | Azure
description: Узнайте, как одностраничные приложения JavaScript могут вызывать API, которым требуются маркеры доступа от конечной точки Azure Active Directory версии 2.0.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3576c015fcca7eb9c390f38c6527d76353efe06
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919398"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Вход пользователей и вызов API Microsoft Graph из одностраничного приложения JavaScript (SPA)

В этом руководстве описано, как с помощью одностраничного приложения (SPA) JavaScript сделать следующее:
- войти в личные, рабочие и учебные учетные записи; 
- Получение маркера доступа
- вызвать API Microsoft Graph и другие API, которым требуются маркеры доступа от *конечной точки платформы удостоверений Майкрософт*.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Как работает пример приложения, созданный в этом руководстве

![Схема работы примера приложения, создаваемого в этом кратком руководстве](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Дополнительные сведения

Пример приложения, созданный с помощью этого руководства, позволяет одностраничному приложению JavaScript выполнять запрос к API Microsoft Graph или веб-API, принимающему маркеры от конечной точки платформы удостоверений Майкрософт. В этом сценарии после входа пользователя в систему маркер доступа запрашивается и добавляется в HTTP-запросы с использованием заголовка авторизации. Получение маркера и его обновление выполняет библиотека проверки подлинности Майкрософт (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Библиотеки

В этом руководстве используется следующая библиотека:

|Библиотека|ОПИСАНИЕ|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Библиотека аутентификации Майкрософт для JavaScript (предварительная версия)|

> [!NOTE]
> В качестве целевого объекта в библиотеке *msal.js* задана конечная точка платформы удостоверений Майкрософт. Это позволяет выполнять вход и запрашивать маркеры, используя личные, рабочие и учебные учетные записи. К конечной точке платформы удостоверений Майкрософт применяются [некоторые ограничения](azure-ad-endpoint-comparison.md#limitations).
> См. [сравнение конечных точек версии 1.0 и 2.0](azure-ad-endpoint-comparison.md).

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Настройка веб-сервера или проекта

> Хотите скачать этот пример проекта вместо указанного выше проекта? Выполните одно из приведенных ниже действий.
> 
> - Чтобы запустить проект с помощью локального веб-сервера, например Node.js, [скачайте файлы проекта](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - (Необязательно.) Чтобы запустить проект с помощью сервера IIS (Microsoft Internet Information Services), [скачайте проект Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> Перейдите к [настройке](#register-your-application), чтобы настроить пример кода перед выполнением.

## <a name="prerequisites"></a>Предварительные требования

* Для работы с этим учебником требуется локальный веб-сервер, например [Node.js](https://nodejs.org/en/download/) или [.NET Core](https://www.microsoft.com/net/core), либо интеграция IIS Express с [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Если вы используете Node.js для запуска проекта, установите интегрированную среду разработки, например [Visual Studio Code](https://code.visualstudio.com/download), чтобы изменять файлы проекта.

* Инструкции в этом учебнике основаны на Node.js и Visual Studio 2017, но вы можете использовать любую другую среду разработки или веб-сервер.

## <a name="create-your-project"></a>Создание проекта

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Вариант 1. Node.js или другие веб-серверы
> Установите [Node.js](https://nodejs.org/en/download/) и создайте папку для размещения приложения.
>
> ### <a name="option-2-visual-studio"></a>Вариант 2. Visual Studio
> Если вы используете Visual Studio и создаете новый проект, выполните следующие шаги:
> 1. В Visual Studio выберите **Файл** > **Создать** > **Проект**.
> 1. В **Visual C#\Web** выберите **Веб-приложение ASP.NET (.NET Framework)** .
> 1. Введите имя приложения и нажмите кнопку **ОК**.
> 1. В разделе **Создание веб-приложения ASP.NET** выберите **Пусто**.

## <a name="create-the-spa-ui"></a>Создание пользовательского интерфейса одностраничного приложения
1. Создайте файл *index.html* для своего одностраничного приложения JavaScript. Если вы работаете с Visual Studio, выберите проект (корневую папку проекта). Щелкните правой кнопкой мыши и выберите **Добавить** > **Новый элемент** > **HTML-страница**. Присвойте файлу имя *index.html*.

1. В файле *index.html* добавьте следующий код:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > Приведенную в сценарии выше версию MSAL.js можно заменить последней выпущенной версией в разделе с [выпусками MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Использование библиотеки аутентификации Майкрософт (MSAL) для входа пользователя

Добавьте в файл `index.html` следующий код внутри тегов `<script></script>`:

   ```JavaScript
   var msalConfig = {
       auth: {
           clientId: "Enter_the_Application_Id_here",
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

Когда пользователь впервые нажимает кнопку **Войти**, метод `signIn` вызывает `loginPopup`, чтобы пользователь мог выполнить вход. Этот метод открывает всплывающее окно с *конечной точкой платформы удостоверений Майкрософт*, чтобы запросить и проверить учетные данные пользователя. После успешного входа пользователь перенаправляется обратно на исходную страницу *index.html*. Полученный маркер обрабатывается в `msal.js`, а сведения из него кэшируются. Этот маркер известен как *маркер идентификатора*. Он содержит основные сведения о пользователе, такие как отображаемое имя пользователя. Если вы планируете использовать какие-либо данные, предоставляемые этим маркером, то необходимо убедиться, что маркер проверен внутренним сервером. Это позволит гарантировать, что маркер был выдан допустимому пользователю для вашего приложения.

Одностраничное приложение, создаваемое в рамках этого руководства вызывает `acquireTokenSilent` и (или) `acquireTokenPopup`, чтобы получить *маркер доступа*, используемый при запросе API Microsoft Graph для получения сведений из профиля пользователя. Если вам нужен пример, проверяющий маркер идентификатора, см. [пример приложения на GitHub](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Пример на GitHub: active-directory-javascript-singlepageapp-dotnet-webapi-v2"). В нем для проверки маркеров используется веб-API ASP.NET.

#### <a name="getting-a-user-token-interactively"></a>Интерактивное получение маркера пользователя

После первоначального входа вам не нужно требовать от пользователей повторно выполнять аутентификацию при каждом запросе маркера для доступа к ресурсу. Это означает, что в большинстве случаев для получения маркеров следует использовать *acquireTokenSilent*. Но иногда требуется настроить принудительное взаимодействие пользователей с конечной точкой платформы удостоверений Майкрософт. Примеры приведены ниже.

- Пользователям нужно повторно вводить учетные данные, когда истекает срок действия пароля.
- Приложение запрашивает доступ к ресурсу, на обращение к которому пользователь должен дать согласие.
- Требуется двухфакторная проверка подлинности.

При вызове *acquireTokenPopup* открывается всплывающее окно (или же *acquireTokenRedirect* перенаправляет пользователей на конечную точку платформы идентификации Майкрософт). В этом окне пользователям необходимо подтвердить учетные данные, предоставить согласие на требуемый ресурс или выполнить двухфакторную проверку подлинности.

#### <a name="getting-a-user-token-silently"></a>Автоматическое получение маркера пользователя

Метод `acquireTokenSilent` отвечает за получение и обновление маркера без участия пользователя. После первого выполнения метода `loginPopup` или `loginRedirect` обычно используется метод `acquireTokenSilent`, чтобы получить маркеры для доступа к защищенным ресурсам для последующих вызовов. (Вызовы для запроса или обновления маркеров не требуют взаимодействия с пользователем.) В некоторых случаях выполнение `acquireTokenSilent` может завершаться сбоем. Например, так происходит, когда истекает срок действия пароля пользователя. Приложение может обработать это исключение двумя способами:

1. Немедленно вызвать `acquireTokenPopup`, чтобы отобразить запрос на вход для пользователя. Этот шаблон обычно используется в интерактивных приложениях, где пользователю недоступно не прошедшее проверку подлинности содержимое. Пример, созданный в ходе пошаговой настройки, использует этот шаблон.

2. Приложения также могут визуально уведомить пользователя, что требуется интерактивный вход, чтобы пользователь мог выбрать подходящее время для входа или приложение могло повторить метод `acquireTokenSilent` ​​позднее. Обычно это применимо в тех случаях, когда пользователь может использовать другие функции приложения, на которые это не влияет. Например, в приложении есть содержимое, для доступа к аутентификация не требуется. В этой ситуации пользователь может самостоятельно решить, когда выполнять вход для получения доступа к защищенному ресурсу или обновления устаревших данных.

> [!NOTE]
> При работе с этим кратким руководством методы `loginRedirect` и `acquireTokenRedirect` используются, когда в качестве браузера используется Internet Explorer. Мы соблюдаем такую логику из-за [известной проблемы](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues), связанной с тем, как Internet Explorer отображает всплывающие окна.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Вызов API Microsoft Graph с помощью полученного маркера

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

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Дополнительные сведения о вызове REST через защищенный API

В примере приложения, созданном с помощью этого руководства, метод `callMSGraph()` выполняет HTTP-запрос `GET` к защищенному ресурсу, которому требуется маркер. Затем метод возвращает содержимое вызывающему объекту. Этот метод добавляет полученный маркер в *заголовок авторизации HTTP*. Для примера приложения, созданного с помощью этого руководства, ресурсом является конечная точка *me* из API Microsoft Graph, которая отображает сведения о профиле пользователя.

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

## <a name="register-your-application"></a>Регистрация приложения

1. Войдите на [портале Azure](https://portal.azure.com/).

1. Если ваша учетная запись предоставляет доступ к нескольким клиентам, вверху справа щелкните свою учетную запись и выберите для текущего сеанса работы нужный клиент Azure AD.
1. Перейдите на страницу [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) платформы удостоверений Майкрософт для разработчиков.
1. Когда откроется страница **Register an application** (Регистрация приложения), введите имя приложения.
1. В разделе **Поддерживаемые типы учетных записей** выберите **Accounts in any organizational directory and personal Microsoft accounts** (Учетные записи в любом каталоге организации и личные учетные записи Майкрософт).
1. В разделе **URI перенаправления** выберите в раскрывающемся списке **Веб-платформа**, а затем задайте значение URL-адреса приложения, размещенного на вашем веб-сервере.

   См. сведения о том, как задать и получить URL-адрес перенаправления для Node.js и [Visual Studio](#set-a-redirect-url-for-visual-studio).

1. Выберите **Зарегистрировать**.
1. На странице приложения **Обзор** запишите **идентификатор приложения (клиента)** для использования в будущем.
1. Для этого краткого руководства должно быть включено [неявное предоставление разрешения потока](v2-oauth2-implicit-grant-flow.md). В левой области навигации зарегистрированного приложения выберите **Проверка подлинности**.
1. В окне **Дополнительные параметры** в разделе **Неявное предоставление** установите флажки **Токен идентификатора** и **Маркеры доступа**. Токены идентификатора и маркеры доступа необходимы, так как это приложение должно обрабатывать вход пользователей и вызов API.
1. Щелкните **Сохранить**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Настройка URL-адреса перенаправления для Node.js
> При использовании Node.js порт веб-сервера можно задать в файле *server.js*. В этом руководстве используется порт 30662, но вы можете указать любой другой доступный порт.
>
> Чтобы настроить URL-адрес перенаправления в сведениях о регистрации приложения, снова перейдите на панель **Регистрация приложения** и выполните одно из следующих действий:
>
> - Установите значение *`http://localhost:30662/`* в качестве **URL-адреса перенаправления**.
> - Если вы используете настраиваемый TCP-порт, используйте *`http://localhost:<port>/`* (где *\<порт>*  — это номер пользовательского TCP-порта).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Установка URL-адреса перенаправления для Visual Studio
> Чтобы получить URL-адрес перенаправления для Visual Studio, сделайте следующее:
> 1. В обозревателе решений выберите проект.
>
>    Откроется окно **Свойства**. Если окно не открывается, нажмите клавишу F4.
>
>    ![Окно свойств проекта JavaScriptSPA](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Скопируйте значение **URL-адреса**.
> 1. Вернитесь на панель **Регистрация приложения** и вставьте скопированное значение как **URL-адрес перенаправления**.

#### <a name="configure-your-javascript-spa"></a>Настройка одностраничного приложения JavaScript

1. В файл *index.html*, созданный во время установки проекта, добавьте сведения о регистрации приложения. В верхней части файла в тегах `<script></script>` добавьте следующий код:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Описание
    - *\<Enter_the_Application_Id_here>*  — **идентификатор приложения (клиент)** для зарегистрированного приложения.
    - *\<Enter_the_Tenant_info_here>* присваивается одно из следующих значений:
       - Если приложение поддерживает *учетные записи в этом каталоге организации*, замените это значение **идентификатором клиента** или **именем клиента** (например, *contoso.microsoft.com*).
       - Если ваше приложение поддерживает*Учетные записи в любом каталоге организации*, замените это значение на **организации**.
       - Если приложение поддерживает *учетные записи в любом каталоге организации и личные учетные записи Майкрософт*, замените это значение на **общие**. Чтобы ограничить поддержку только *с помощью личных учетных записей Microsoft*, замените это значение на **пользователей**.

## <a name="test-your-code"></a>Тестирование кода

Протестируйте код с помощью следующих сред.

### <a name="test-with-nodejs"></a>Тестирование кода с Node.js

Если вы не используете Visual Studio, убедитесь, что веб-сервер запущен.

1. Настройте для сервера ожидание передачи данных через TCP-порт на основе расположения файла *index.html*. Чтобы запустить для Node.js веб-сервер для прослушивания порта, выполните следующие команды в командной строке из папки приложения:

    ```bash
    npm install
    node server.js
    ```
1. В браузере введите **http://\<span>\</span>localhost:30662** или **http://\<span>\</span>localhost:{port}** , где *port* соответствует порту, с которого веб-сервер ожидает передачи данных. Появится содержимое файла *index.html* с кнопкой **Sign In** (Войти).

### <a name="test-with-visual-studio"></a>Тестирование кода в Visual Studio

Если вы используете Visual Studio, выберите решение проекта и нажмите клавишу F5, чтобы запустить проект. В браузере откроется адрес http://<span></span>localhost:{port} и отобразится кнопка **Sign In** (Войти).

## <a name="test-your-application"></a>Тестирование приложения

После загрузки файла *index.html* в браузер нажмите кнопку **Sign In** (Войти). Вам будет предложено войти с помощью конечной точки платформы удостоверений Майкрософт.

![Окно входа учетной записи JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Предоставление разрешения на доступ к приложению

При первом входе в приложение вам будет предложено предоставить ему доступ к профилю, а также выполнить вход:

![Окно "Запрошенные разрешения"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Просмотр результатов приложения

После входа сведения о профиле пользователя будут получены в отображаемом ответе API Microsoft Graph:

![Результаты после вызова Microsoft API Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Дополнительные сведения об областях и делегированных разрешениях

Для чтения профиля пользователя API Microsoft Graph требуется область *user.read*. По умолчанию эта область автоматически добавляется в каждое приложение, зарегистрированное на портале регистрации. Для других API Microsoft Graph, а также для пользовательских API вашего внутреннего сервера, могут потребоваться дополнительные области. Например, для отображения списка календарей пользователя API Microsoft Graph требуется область *Calendars.Read*.

Чтобы из контекста приложения получить доступ к календарям пользователя, добавьте делегированное разрешение *Calendars.Read* в сведения о регистрации приложения. Затем добавьте область *Calendars.Read* в вызов `acquireTokenSilent`.

>[!NOTE]
>При увеличении количества областей от пользователя могут потребоваться дополнительные согласия.

Если серверному API не требуется область (мы не рекомендуем использовать такую конфигурацию), вы можете применять в вызовах *clientId* в качестве области для получения маркеров.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Помогите нам улучшить платформу Microsoft Identity. Поделитесь своим мнением, ответив на два вопроса.

> [!div class="nextstepaction"]
> [Опрос по платформе удостоверений Майкрософт](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
