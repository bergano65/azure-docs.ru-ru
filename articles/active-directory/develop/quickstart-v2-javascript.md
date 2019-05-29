---
title: Краткое руководство. Использование приложений JavaScript с платформой удостоверений Майкрософт | Azure
description: Узнайте, как приложения JavaScript могут вызывать API, которому необходимы маркеры доступа от платформы удостоверений Майкрософт.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9deaf610696f676610f589168426ac24be692c99
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823518"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application-spa"></a>Краткое руководство. Выполнение входа пользователей и получение маркера доступа от одностраничного приложения JavaScript (SPA)

Из этого краткого руководства вы узнаете, как использовать пример кода, демонстрирующий, как одностраничное приложение (SPA) JavaScript может выполнять вход с помощью личных, рабочих и учебных учетных записей, а также как получить маркер доступа, чтобы вызывать API Microsoft Graph или любой другой веб-API.

![Схема работы приложения, создаваемого в этом кратком руководстве](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством необходимо сделать следующее:
* Чтобы запускать проект на сервере Node.js:
    * Установите [Node.js](https://nodejs.org/en/download/)
    * Установите [Visual Studio Code](https://code.visualstudio.com/download) для внесения правок в файлы проекта.
* Чтобы запустить проект как решение Visual Studio, установите [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Регистрация и скачивание приложения, используемого в этом кратком руководстве
> У вас есть два варианта запуска приложения, используемого в этом кратком руководстве:
> * [Экспресс-способ] [Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Вручную] [Вариант 2. Регистрация и настройка приложения и примера кода вручную](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода
>
> 1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
> 1. Если учетная запись предоставляет доступ нескольким клиентам, выберите свою учетную запись в правом верхнем углу и нужный клиент Azure AD для этого сеанса портала.
> 1. Откройте на [портале Azure новую панель регистрации приложений](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Введите имя своего приложения и щелкните **Зарегистрировать**.
> 1. Следуйте инструкциям для загрузки и автоматической настройки нового приложения одним щелчком мыши.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Вариант 2. Регистрация и настройка приложения и примера кода вручную
>
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
>
> 1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
> 1. Если учетная запись предоставляет доступ нескольким клиентам, выберите свою учетную запись в правом верхнем углу и нужный клиент Azure AD для этого сеанса портала.
> 1. Перейдите на страницу [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) Платформы удостоверений Майкрософт для разработчиков.
> 1. Выберите **Новая регистрация**.
> 1. Когда откроется страница **Register an application** (Регистрация приложения), введите имя приложения.
> 1. В разделе **Поддерживаемые типы учетных записей** выберите **Accounts in any organizational directory and personal Microsoft accounts** (Учетные записи в любом каталоге организации и личные учетные записи Майкрософт).
> 1. Выберите платформу **Веб** в разделе **URI перенаправления** и установите значение `http://localhost:30662/`.
> 1. По завершении щелкните **Зарегистрировать**.  На странице приложения **Обзор** запишите **идентификатор приложения (клиента)** .
> 1. Для этого краткого руководства должно быть включено [неявное предоставление разрешения потока](v2-oauth2-implicit-grant-flow.md). В левой области навигации зарегистрированного приложения выберите **Проверка подлинности**.
> 1. В окне **Дополнительные параметры** в разделе **Неявное предоставление** установите флажки **Токен идентификатора** и **Маркеры доступа**. Токены идентификатора и маркеры доступа необходимы, так как это приложение должно выполнять вход пользователей и вызов API.
> 1. Щелкните **Сохранить**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Шаг 1. Настройка приложения на портале Azure
> Для работы примера кода в этом кратком руководстве необходимо добавить URL-адрес перенаправления `http://localhost:30662/` и включить **неявное предоставление разрешения**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести эти изменения для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-javascript/green-check.png). Ваше приложение настроено с помощью этих атрибутов.

#### <a name="step-2-download-the-project"></a>Шаг 2. Скачивание проекта

Вы можете выбрать один из этих вариантов, подходящий для вашей среды разработки.
* [Скачайте основные файлы проекта](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip), чтобы запустить их с помощью веб-сервера, используя Node.js. Чтобы открыть файлы, используйте редактор, например [Visual Studio Code](https://code.visualstudio.com/).

* (Необязательно) [Загрузите проект Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip), чтобы запустить с помощью сервера IIS. Извлеките ZIP-файл в локальную папку (например, **C:\Azure-Samples**).



#### <a name="step-3-configure-your-javascript-app"></a>Шаг 3. Настройка приложения JavaScript

> [!div renderon="docs"]
> В папке *JavaScriptSPA* измените `index.html` и задайте значения `clientID` и `authority` в разделе `msalConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> В папке *JavaScriptSPA* измените `index.html` и замените `msalConfig` следующим:

```javascript
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

```
> [!div renderon="docs"]
>
> Описание
> - `Enter_the_Application_Id_here` — это **идентификатор приложения (клиента)** , которое вы зарегистрировали.
> - `Enter_the_Tenant_Info_Here` может иметь несколько значений.
>   - Если приложение поддерживает **учетные записи только в этом каталоге организации**, замените это значение **идентификатором клиента** или **именем клиента** (например, contoso.microsoft.com).
>   - Если ваше приложение поддерживает вариант **Учетные записи в любом каталоге организации**, замените это значение на `organizations`.
>   - Если приложение поддерживает **учетные записи в любом каталоге организации и личные учетные записи Майкрософт**, замените это значение на `common`. Чтобы ограничить поддержку только *личными учетными записями Microsoft*, замените это значение на `consumers`.
>
> > [!TIP]
> > Чтобы найти значения параметров **Идентификатор приложения (клиента)** , **Идентификатор каталога (клиента)** и **Поддерживаемые типы учетных записей**, на портале Azure перейдите на страницу **Обзор**.
>

#### <a name="step-4-run-the-project"></a>Шаг 4. Запуск проекта

* Если вы используете [Node.js](https://nodejs.org/en/download/), сделайте следующее:

    1. В каталоге проекта выполните следующую команду, чтобы запустить сервер.

        ```batch
        npm install
        node server.js
        ```

    1. Откройте браузер и перейдите по ссылке `http://localhost:30662/`.
    1. Нажмите кнопку **Войти**, чтобы войти в систему и вызывать API Microsoft Graph.


* Если вы используете **Visual Studio**, выберите решение проекта и нажмите клавишу [F5](https://visualstudio.microsoft.com/downloads/), чтобы запустить проект.

Когда приложение загрузится в браузере, щелкните **Войти**.  После первого входа предоставьте приложению разрешение на использование данных вашего профиля для входа. После успешного входа на странице отобразятся данные вашего профиля пользователя.

## <a name="more-information"></a>Дополнительные сведения

### <a name="msaljs"></a>*msal.js*

MSAL — это библиотека, используемая для выполнения входа пользователей и запросов маркеров, которые нужны для доступа к API, защищенному платформой удостоверений Майкрософт. В файле *index.html*, используемом в кратком руководстве, содержится ссылка на библиотеку:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
```
> [!TIP]
> Приведенную выше версию можно заменить последней выпущенной версией в разделе [MSAL.js releases](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


Кроме того, если вы установили Node, последнюю версию можно скачать с помощью npm:

```batch
npm install msal
```

### <a name="msal-initialization"></a>Инициализация MSAL

В примере кода ниже показано, как инициализировать библиотеку:

```javascript
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

var myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Where  |  |
> |---------|---------|
> |`ClientId`     |Идентификатор приложения, зарегистрированного на портале Azure|
> |`authority`    | (Необязательно) Это — URL-адрес центра сертификации, который был описан выше в разделе конфигурации, предназначенный для поддержки типов учетных записей. По умолчанию центром является `https://login.microsoftonline.com/common`. |
> |`cacheLocation`  | (Необязательно) Этот параметр настраивает хранилище браузера для состояния проверки подлинности. По умолчанию используется sessionStorage.   |
> |`storeAuthStateInCookie`  | (Необязательно) Библиотека будет хранить состояние запроса проверки подлинности, необходимое для проверки потоков аутентификации, в файлах cookie браузера. Параметр установлен для браузеров IE и Edge, чтобы уменьшить определенные [известные проблемы](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

 Подробные сведения о доступных настраиваемых параметрах см. в статье [Initialize client applications using MSAL.js](msal-js-initializing-client-applications.md) (Инициализация клиентских приложений с помощью MSAL.js).

### <a name="sign-in-users"></a>Выполнение входа пользователей

В приведенном ниже фрагменте кода показано, как выполнить вход пользователей:

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | (Необязательно.) Содержит области, для которых запрашивается согласие пользователя во время входа в систему. Например, `[ "user.read" ]` для Microsoft Graph или `[ "<Application ID URL>/scope" ]` для пользовательских веб-API (то есть `api://<Application ID>/access_as_user`). |

> [!TIP]
> Также можно использовать метод `loginRedirect`, чтобы с текущей страницы выполнялось перенаправление на страницу входа вместо отображения всплывающего окна.

### <a name="request-tokens"></a>Запрос маркеров

В MSAL есть три метода получения маркеров: `acquireTokenRedirect`, `acquireTokenPopup` и `acquireTokenSilent`.

#### <a name="get-a-user-token-silently"></a>Автоматическое получение маркера пользователя

Метод `acquireTokenSilent` обрабатывает получение и обновление маркера без участия пользователя. После первого выполнения метода `loginRedirect` или `loginPopup` обычно используется метод `acquireTokenSilent`, чтобы получить маркеры для доступа к защищенным ресурсам для последующих вызовов. Вызовы для запроса или обновления маркеров выполняются автоматически.

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | Содержит запрашиваемые области, которые должны быть возвращены в маркере доступа для API. Например, `[ "user.read" ]` для Microsoft Graph или `[ "<Application ID URL>/scope" ]` для пользовательских веб-API (то есть `api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Интерактивное получение маркера пользователя

Иногда требуется настроить принудительное взаимодействие пользователей с конечной точкой платформы удостоверений Майкрософт. Например: 
* Пользователям может потребоваться повторно ввести учетные данные, так как истек срок действия пароля.
* Ваше приложение запрашивает доступ к дополнительным областям ресурса, на обращение к которым пользователь должен дать согласие.
* Требуется двухфакторная проверка подлинности

Обычный рекомендуемый шаблон для большинства приложений — вызов `acquireTokenSilent`, перехват исключения, а затем вызов `acquireTokenPopup` (или `acquireTokenRedirect`) для запуска интерактивного запроса.

После вызова `acquireTokenPopup` отображается всплывающее окно для входа (или `acquireTokenRedirect` перенаправляет пользователей на конечную точку платформы удостоверений Майкрософт), где пользователям нужно подтвердить свои учетные данные, предоставить согласие на доступ к требуемому ресурсу или пройти двухфакторную проверку подлинности.

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

> [!NOTE]
> В этом кратком руководстве для Internet Explorer используются методы `loginRedirect` и `acquireTokenRedirect` из-за [известной проблемы](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) с обработкой всплывающих окон в Internet Explorer.

## <a name="next-steps"></a>Дополнительная информация

Более подробные инструкции по созданию приложения для этого краткого руководства можно найти в приведенном ниже руководстве по JavaScript.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Подробнее о шагах, необходимых для создания приложения для этого краткого руководства

> [!div class="nextstepaction"]
> [Tutorial to sign in and call MS Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa) (Руководство по вызову API Microsoft Graph)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Ознакомление с документацией, часто задаваемыми вопросами, проблемами и многим другим в репозитории MSAL

> [!div class="nextstepaction"]
> [Библиотека проверки подлинности Майкрософт для JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js)
