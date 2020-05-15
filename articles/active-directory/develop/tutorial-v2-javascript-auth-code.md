---
title: Руководство по работе с одностраничным приложением JavaScript 2.0 (платформа удостоверений Майкрософт) | Azure
description: Узнайте, как одностраничные приложения JavaScript могут через поток кода авторизации вызывать API, которым требуются маркеры доступа от конечной точки Azure Active Directory версии 2.0.
services: active-directory
documentationcenter: dev-center-name
author: hahamil
manager: CelesteDG
ROBOTS: NOINDEX
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f664f5fa4219a8bcc32230b352e90cc2516faceb
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890385"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa---msaljs-20"></a>Вход пользователей и вызов API Microsoft Graph из одностраничного приложения JavaScript (SPA) с помощью MSAL.js 2.0

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

Для версии MSAL.js, используемой в этом примере, применяется поток кода авторизации OAuth 2.0 с поддержкой PKCE. Дополнительные сведения об этом протоколе, а также о различиях между неявным потоком кода и потоком кода авторизации см. в [документации](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow). Если вам нужно руководство по использованию неявного потока, перейдите к [руководству по MSAL.js версии 1](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-spa).

В этой версии MSAL.js улучшена текущая библиотека msal-core и используется поток кода авторизации в браузере. В этой версии доступны почти все функции старой библиотеки, но обе версии имеют ряд особенностей в потоке проверки подлинности. Эта версия **НЕ** поддерживает неявный поток.

В этом руководстве описано, как с помощью одностраничного приложения (SPA) JavaScript сделать следующее:
- войти в личные, рабочие и учебные учетные записи;
- Получение маркера доступа
- вызвать API Microsoft Graph и другие API, которым требуются маркеры доступа от *конечной точки платформы удостоверений Майкрософт*.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Как работает пример приложения, созданный в этом руководстве

![Схема работы примера приложения, создаваемого в этом кратком руководстве](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>Дополнительные сведения

Пример приложения, созданный с помощью этого руководства, позволяет одностраничному приложению JavaScript выполнять запрос к API Microsoft Graph или веб-API, принимающему маркеры от конечной точки платформы удостоверений Майкрософт. В этом сценарии после входа пользователя в систему маркер доступа запрашивается и добавляется в HTTP-запросы с использованием заголовка авторизации. Получение маркера и его обновление выполняет библиотека проверки подлинности Майкрософт (MSAL).

### <a name="libraries"></a>Библиотеки

В этом руководстве используется следующая библиотека:

|Библиотека|Описание|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|Библиотека проверки подлинности Майкрософт для пакета JavaScript для браузера|

## <a name="set-up-your-web-server-or-project"></a>Настройка веб-сервера или проекта

Хотите скачать этот пример проекта вместо указанного выше проекта? Чтобы запустить проект на локальном веб-сервере, например Node.js, клонируйте файлы проекта.

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Перейдите к [настройке](#register-your-application), чтобы настроить пример кода перед выполнением.

## <a name="prerequisites"></a>Предварительные требования

* Для работы с этим руководством вам потребуется локальный веб-сервер, например [Node.js](https://nodejs.org/en/download/) или [.NET Core](https://www.microsoft.com/net/core).

* Если вы используете Node.js для запуска проекта, установите интегрированную среду разработки, например [Visual Studio Code](https://code.visualstudio.com/download), чтобы изменять файлы проекта.

* Инструкции в этом учебнике предполагают использование Node.js.

## <a name="create-your-project"></a>Создание проекта

Установите [Node.js](https://nodejs.org/en/download/) и создайте папку для размещения приложения. Теперь реализуйте небольшой веб-сервер [Express](https://expressjs.com/) для обслуживания файла `index.html`.

1. Сначала перейдите в окне терминала в папку проекта, затем выполните следующие команды NPM.
    ```console
    npm init -y
    npm install @azure/msal-Browser
    npm install express
    ```
2. Создайте JS-файл с именем *server.js* и добавьте в него следующий код:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

Теперь у вас есть простой сервер для обслуживания одностраничного приложения (SPA). Ниже приведена предполагаемая структура папок после выполнения инструкций этого учебника.

![изображение с текстом предполагаемой структуры папок SPA](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Создание пользовательского интерфейса одностраничного приложения

1. Создайте файл *index.html* для одностраничного приложения JavaScript в папке *app*. Этот файл используется для реализации пользовательского интерфейса, созданного на **платформе Bootstrap 4**, и импорта файлов скриптов для настройки, проверки подлинности и вызовов API.

   В файле *index.html* добавьте следующий код:

   ```html
   <!DOCTYPE html>
   <html lang="en">
      <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
         <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

         <!-- IE support: add promises polyfill before msal.js  -->
         <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js"></script>

         <!-- adding Bootstrap 4 for UI components  -->
         <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
         <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
         <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand" href="/">MS Identity Platform</a>
            <div class="btn-group ml-auto dropleft">
               <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                  Sign In
               </button>
            </div>
         </nav>
         <br>
         <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
         <br>
         <div class="row" style="margin:auto" >
         <div id="card-div" class="col-md-3" style="display:none">
         <div class="card text-center">
            <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mails</button>
            </div>
         </div>
         </div>
         <br>
         <br>
            <div class="col-md-4">
            <div class="list-group" id="list-tab" role="tablist">
            </div>
            </div>
            <div class="col-md-5">
             <div class="tab-content" id="nav-tabContent">
            </div>
            </div>
         </div>
         <br>
         <br>

         <!-- importing bootstrap.js and supporting js libraries -->
         <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
         <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
         <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

         <!-- importing app scripts (load order is important) -->
         <script type="text/javascript" src="./authConfig.js"></script>
         <script type="text/javascript" src="./graphConfig.js"></script>
         <script type="text/javascript" src="./ui.js"></script>

         <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
         <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
         <script type="text/javascript" src="./authPopup.js"></script>
         <script type="text/javascript" src="./graph.js"></script>
      </body>
     </html>
     ```

   > [!TIP]
   > Приведенную в сценарии выше версию MSAL.js можно заменить последней выпущенной версией в разделе с [выпусками MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


2. Теперь создайте JS-файл с именем *ui.js* для получения доступа к элементам DOM и их обновления, а также добавьте следующий код:

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
   }

   function updateUI(data, endpoint) {
     console.log('Graph API responded at: ' + new Date().toString());

     if (endpoint === graphConfig.graphMeEndpoint) {
       const title = document.createElement('p');
       title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
       const email = document.createElement('p');
       email.innerHTML = "<strong>Mail: </strong>" + data.mail;
       const phone = document.createElement('p');
       phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
       const address = document.createElement('p');
       address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
       profileDiv.appendChild(title);
       profileDiv.appendChild(email);
       profileDiv.appendChild(phone);
       profileDiv.appendChild(address);

     } else if (endpoint === graphConfig.graphMailEndpoint) {
         if (data.value.length < 1) {
           alert("Your mailbox is empty!")
         } else {
           const tabList = document.getElementById("list-tab");
           tabList.innerHTML = ''; // clear tabList at each readMail call
           const tabContent = document.getElementById("nav-tabContent");

           data.value.map((d, i) => {
             // Keeping it simple
             if (i < 10) {
               const listItem = document.createElement("a");
               listItem.setAttribute("class", "list-group-item list-group-item-action")
               listItem.setAttribute("id", "list" + i + "list")
               listItem.setAttribute("data-toggle", "list")
               listItem.setAttribute("href", "#list" + i)
               listItem.setAttribute("role", "tab")
               listItem.setAttribute("aria-controls", i)
               listItem.innerHTML = d.subject;
               tabList.appendChild(listItem)

               const contentItem = document.createElement("div");
               contentItem.setAttribute("class", "tab-pane fade")
               contentItem.setAttribute("id", "list" + i)
               contentItem.setAttribute("role", "tabpanel")
               contentItem.setAttribute("aria-labelledby", "list" + i + "list")
               contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
               tabContent.appendChild(contentItem);
             }
           });
         }
     }
   }
   ```

## <a name="register-your-application"></a>Регистрация приложения

Выполните [эти инструкции, чтобы зарегистрировать новое одностраничное приложение](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration).

#### <a name="set-a-redirect-url-for-nodejs"></a>Настройка URL-адреса перенаправления для Node.js

При использовании Node.js порт веб-сервера можно задать в файле *server.js*. В этом учебнике используется порт 3000, но вы можете указать любой другой доступный порт.

Чтобы настроить URL-адрес перенаправления в сведениях о регистрации приложения, вернитесь на панель **Регистрация приложения** и зарегистрируйте новое **одностраничное приложение**, выполнив одно из следующих действий:

- Установите значение *`http://localhost:3000/`* в качестве **URL-адреса перенаправления**.
- Если вы используете настраиваемый TCP-порт, используйте *`http://localhost:<port>/`* (где *\<порт>*  — это номер пользовательского TCP-порта).

### <a name="configure-your-javascript-spa"></a>Настройка одностраничного приложения JavaScript

Создайте JS-файл с именем *authConfig.js*, который будет содержать параметры конфигурации для проверки подлинности, и добавьте в него следующий код:

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
   scopes: ["openid", "profile", "User.Read"]
  };

   // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
   scopes: ["User.Read", "Mail.Read"]
  };

```

 Где:
 - *\<Enter_the_Application_Id_Here>*  — **Идентификатор приложения (клиент)** для зарегистрированного приложения.
 - *\<Enter_the_Cloud_Instance_Id_Here>*  — экземпляр облака Azure. Для основного или глобального облака Azure просто введите *https://login.microsoftonline.com* . Сведения для **национальных** облаков (например, Китая) см. в [этой статье](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
 - *\<Enter_the_Tenant_info_here>* присваивается одно из следующих значений:
   - Если приложение поддерживает *учетные записи только в этом каталоге организации*, замените это значение **идентификатором клиента** или **именем клиента** (например, *contoso.microsoft.com*).
   - Если ваше приложение поддерживает *учетные записи в любом каталоге организации*, замените это значение на **organizations**.
   - Если приложение поддерживает *учетные записи в любом каталоге организации и личные учетные записи Майкрософт*, замените это значение на **common**. Чтобы ограничить поддержку только *личными учетными записями Майкрософт*, замените это значение на **consumers**.
- Строка *\Enter_the_Redirect_Uri_Here>* обозначает порт, который вы указали при регистрации на портале ( *`http://localhost:3000/`* )


Создайте JS-файл с именем `graphConfig.js`, который будет содержать параметры конфигурации для вызова API Microsoft Graph, и добавьте следующий код:
```javascript
// Add here the endpoints for MS Graph API services you would like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
};
```
- *\<Enter_the_Graph_Endpoint_Here >*  — экземпляр API Microsoft Graph. Для глобальной конечной точки API Microsoft Graph просто замените эту строку на `https://graph.microsoft.com`. Сведения о национальных облачных развертываниях см. [здесь](https://docs.microsoft.com/graph/deployments).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Использование библиотеки аутентификации Майкрософт (MSAL) для входа пользователя

### <a name="popup"></a>Всплывающее окно
Создайте JS-файл с именем `authPopup.js`, который будет содержать логику проверки подлинности и получения маркера для всплывающего окна входа, и добавьте в него следующий код:

   ```JavaScript
  // Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

function signIn() {
    myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log('id_token acquired at: ' + new Date().toString());

            if (myMSALObj.getAccount()) {
                showWelcomeMessage(myMSALObj.getAccount());
            }
        }).catch(error => {
            console.error(error);
        });
}

function signOut() {
    myMSALObj.logout();
}

function getTokenPopup(request) {
    return myMSALObj.acquireTokenSilent(request)
        .catch(error => {
            console.warn(error);
            console.warn("silent token acquisition fails. acquiring token using popup");

            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request)
                .then(tokenResponse => {
                    return tokenResponse;
                }).catch(error => {
                    console.error(error);
                });
        });
}

function seeProfile() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(loginRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.classList.add('d-none');
                mailButton.classList.remove('d-none');
            }).catch(error => {
                console.error(error);
            });
    }
}

function readMail() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(tokenRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
            }).catch(error => {
                console.error(error);
            });
    }
}
   ```
### <a name="redirect"></a>Перенаправление
Создайте JS-файл с именем `authRedirect.js`, который будет содержать логику проверки подлинности и получения маркера для перенаправления входа, и добавьте в него следующий код:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;

// Register Callbacks for Redirect flow
myMSALObj.handleRedirectCallback(authRedirectCallBack);

function authRedirectCallBack(error, response) {
    if (error) {
        console.error(error);
    } else {
        if (myMSALObj.getAccount()) {
            console.log('id_token acquired at: ' + new Date().toString());
            showWelcomeMessage(myMSALObj.getAccount());
            getTokenRedirect(loginRequest);
        } else if (response.tokenType === "Bearer") {
            console.log('access_token acquired at: ' + new Date().toString());
        } else {
            console.log("token type is:" + response.tokenType);
        }
    }
}

// Redirect: once login is successful and redirects with tokens, call Graph API
if (myMSALObj.getAccount()) {
    showWelcomeMessage(myMSALObj.getAccount());
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    myMSALObj.logout();
}

// This function can be removed if you do not need to support IE
function getTokenRedirect(request) {
    return myMSALObj.acquireTokenSilent(request)
        .then((response) => {
            console.log(response);
            if (response.accessToken) {
                console.log('access_token acquired at: ' + new Date().toString());
                accessToken = response.accessToken;

                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.style.display = 'none';
                mailButton.style.display = 'initial';
            }
        })
        .catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenRedirect(request);
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest);
}

function readMail() {
    getTokenRedirect(tokenRequest);
}
```

### <a name="more-information"></a>Дополнительные сведения

Когда пользователь впервые нажимает кнопку **Войти**, метод `signIn` вызывает `loginPopup`, чтобы пользователь мог выполнить вход. Этот метод открывает всплывающее окно с *конечной точкой платформы удостоверений Майкрософт*, чтобы запросить и проверить учетные данные пользователя. После успешного входа *msal.js* инициирует [поток кода авторизации](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).

На этом этапе код авторизации, защищенный с помощью PKCE, отправляется в конечную точку маркеров, защищенную с помощью CORS, и обменивается на маркеры. В ответ *msal.js* получает маркер идентификатора, маркер доступа и маркер обновления, обрабатывает их и кэширует информацию из этих маркеров.

Маркер идентификатора содержит основные сведения о пользователе, например отображаемое имя. Если вы планируете использовать какие-либо данные, предоставляемые этим маркером, его необходимо проверить на внутреннем сервере, чтобы гарантировать, что маркер был выдан допустимому пользователю для вашего приложения. Срок действия маркера обновления ограничен и истекает через 24 часа. Маркер обновления можно использовать для автоматического получения новый маркеров доступа.

Одностраничное приложение, создаваемое в рамках этого руководства вызывает `acquireTokenSilent` и (или) `acquireTokenPopup`, чтобы получить *маркер доступа*, используемый при запросе API Microsoft Graph для получения сведений из профиля пользователя. Если вам нужен пример с проверкой маркера идентификатора, ознакомьтесь с примером приложения [active-directory-javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) на сайте GitHub. В нем для проверки маркеров используется веб-API ASP.NET.

#### <a name="get-a-user-token-interactively"></a>Интерактивное получение маркера пользователя

После первого входа не следует требовать от пользователей повторную аутентификацию при каждом запросе маркера для доступа к ресурсу. Чтобы избежать запросов на повторную аутентификацию, используйте `acquireTokenSilent`. Но иногда требуется настроить принудительное взаимодействие пользователей с конечной точкой платформы удостоверений Майкрософт. Пример:

- Пользователям нужно повторно вводить учетные данные, когда истекает срок действия пароля.
- Приложение запрашивает доступ к ресурсу, на обращение к которому пользователь должен дать согласие.
- Требуется двухфакторная проверка подлинности.

При вызове `acquireTokenPopup` открывается всплывающее окно (или `acquireTokenRedirect` перенаправляет пользователей на конечную точку платформы удостоверений Майкрософт). В этом окне пользователям необходимо подтвердить учетные данные, предоставить согласие на требуемый ресурс или выполнить двухфакторную проверку подлинности.

#### <a name="get-a-user-token-silently"></a>Автоматическое получение маркера пользователя

Метод `acquireTokenSilent` отвечает за получение и обновление маркера без участия пользователя. После первого выполнения метода `loginPopup` или `loginRedirect` обычно используется метод `acquireTokenSilent`, чтобы получить маркеры для доступа к защищенным ресурсам для последующих вызовов. (Вызовы для запроса или обновления маркеров не требуют взаимодействия с пользователем.) В некоторых случаях выполнение `acquireTokenSilent` может завершаться сбоем. Например, так происходит, когда истекает срок действия пароля пользователя. Приложение может обработать это исключение двумя способами:

1. Немедленно вызвать `acquireTokenPopup`, чтобы активировать запрос на вход для пользователя. Этот шаблон обычно используется в интерактивных приложениях, где пользователю недоступно не прошедшее проверку подлинности содержимое. Пример, созданный в ходе пошаговой настройки, использует этот шаблон.

1. Визуально уведомить пользователя, что требуется интерактивный вход, чтобы пользователь мог выбрать подходящее время для входа или приложение могло повторить метод `acquireTokenSilent` ​​позднее. Обычно этот способ применим в тех случаях, когда пользователь может использовать другие функции приложения, на которые это не влияет. Например, в приложении есть содержимое, для доступа к аутентификация не требуется. В этой ситуации пользователь может самостоятельно решить, когда выполнять вход для получения доступа к защищенному ресурсу или обновления устаревших данных.

> [!NOTE]
> В этом кратком руководстве по умолчанию используются методы `loginPopup` и `acquireTokenPopup`. Если в качестве браузера вы используете Internet Explorer, мы рекомендуем использовать методы `loginRedirect` и `acquireTokenRedirect` в связи с [известной проблемой](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) со способом работы всплывающих окон Internet Explorer. Если вы хотите узнать, как добиться того же результата с помощью методов перенаправления, изучите документацию по [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Вызов API Microsoft Graph с помощью полученного маркера


 Создайте JS-файл с именем *graph.js* для отправки вызова REST к API Microsoft Graph, и добавьте в него следующий код:

   ```javascript

// Helper function to call MS Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Дополнительные сведения о вызове REST через защищенный API

В примере приложения, созданном с помощью этого руководства, метод `callMSGraph()` выполняет HTTP-запрос `GET` к защищенному ресурсу, которому требуется маркер. Затем метод возвращает содержимое вызывающему объекту. Этот метод добавляет полученный маркер в *заголовок авторизации HTTP*. Для примера приложения, созданного с помощью этого руководства, ресурсом является конечная точка *me* из API Microsoft Graph, которая отображает сведения о профиле пользователя.

## <a name="test-your-code"></a>Тестирование кода

1. На Node.js запустите веб-сервер, выполнив следующие команды в командной строке из папки приложения:

   ```bash
   npm install
   npm start
   ```
1. В браузере введите **http://localhost:3000** или **http://localhost:{port}** (*port* — это порт, прослушивающий ваш веб-сервер). Появится содержимое файла *index.html* с кнопкой **Sign In** (Войти).

## <a name="test-your-application"></a>Тестирование приложения

После загрузки файла *index.html* в браузер нажмите кнопку **Sign In** (Войти). Вам будет предложено войти с помощью конечной точки платформы удостоверений Майкрософт.

![Окно входа учетной записи JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Предоставление разрешения на доступ к приложению

При первом входе в приложение вам будет предложено предоставить ему доступ к профилю, а также выполнить вход:

![Окно "Запрошенные разрешения"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Просмотр результатов приложения

После входа сведения о профиле пользователя будут получены в отображаемом ответе API Microsoft Graph:

![Результаты после вызова Microsoft API Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Дополнительные сведения об областях и делегированных разрешениях

Для чтения профиля пользователя API Microsoft Graph требуется область *user.read*. По умолчанию эта область автоматически добавляется в каждое приложение, зарегистрированное на портале Azure. Для других API Microsoft Graph, а также для пользовательских API вашего внутреннего сервера, могут потребоваться дополнительные области. Например, для отображения сообщений электронной почты пользователя API Microsoft Graph требуется область *Mail.Read*.

> [!NOTE]
> При добавлении областей от пользователя могут потребоваться дополнительные согласия.

Если серверному API не требуется область (мы не рекомендуем использовать такую конфигурацию), вы можете применять в вызовах *clientId* в качестве области для получения маркеров.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

Репозиторий GitHub [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js) содержит дополнительную документацию по библиотеке, часто задаваемые вопросы и предоставляет поддержку по устранению неполадок.
