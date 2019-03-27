---
title: Краткое руководство по Azure Active Directory версии 2 для JavaScript | Документация Майкрософт
description: Узнайте, как приложения JavaScript могут вызывать API, которому необходимы маркеры доступа, с помощью конечной точки Azure Active Directory версии 2.0.
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
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe8c2287da7a7eabc26ff134d8bb44c5e45085f1
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203053"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>Краткое руководство. Выполнение входа пользователей и получение маркера доступа из приложения JavaScript

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Из этого краткого руководства вы узнаете, как использовать пример кода, демонстрирующий, как одностраничное приложение (SPA) JavaScript может выполнять вход с помощью личных, рабочих и учебных учетных записей, а также получать маркер доступа, чтобы вызывать API Microsoft Graph или любой другой веб-API.

![Схема работы приложения, создаваемого в этом кратком руководстве](media/quickstart-v2-javascript/javascriptspa-intro-updated.png)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Регистрация и скачивание приложения, используемого в этом кратком руководстве
> У вас есть два варианта запуска приложения, используемого в этом кратком руководстве:
> * [Экспресс-способ] [Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Вручную] [Вариант 2. Регистрация и настройка приложения и примера кода вручную](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода
>
> 1. Откройте [Регистрация приложений (предварительная версия)](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) на портале Azure.
> 1. Введите имя своего приложения и щелкните **Зарегистрировать**.
> 1. Следуйте инструкциям для загрузки и автоматической настройки нового приложения одним щелчком мыши.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Вариант 2. Регистрация и настройка приложения и примера кода вручную
>
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
>
> 1. Чтобы зарегистрировать приложение, войдите на [портал Azure](https://portal.azure.com/).
> 1. Если учетная запись предоставляет доступ нескольким клиентам, выберите свою учетную запись в правом верхнем углу и нужный клиент Azure AD для этого сеанса портала.
> 1. В области навигации слева выберите службу **Azure Active Directory**, а затем выберите **App registrations (Preview) (Регистрация приложений (предварительная версия)) > Новая регистрация**.
> 1. Когда откроется страница **Register an application** (Регистрация приложения), введите имя приложения.
> 1. В разделе **Поддерживаемые типы учетных записей** выберите **Accounts in any organizational directory and personal Microsoft accounts** (Учетные записи в любом каталоге организации и личные учетные записи Майкрософт).
> 1. Выберите платформу **Веб** в разделе **URI перенаправления** и установите значение `http://localhost:30662/`.
> 1. По завершении щелкните **Зарегистрировать**.  На странице приложения **Обзор** запишите **идентификатор приложения (клиента)**.
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
* [Скачайте основные файлы проекта для веб-сервера, например Node.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
* [Скачайте проект Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

Извлеките ZIP-файл в локальную папку (например, **C:\Azure-Samples**).
Чтобы открыть файлы в папке, используйте редактор, например [Visual Studio Code](https://code.visualstudio.com/).

#### <a name="step-3-configure-your-javascript-app"></a>Шаг 3. Настройка приложения JavaScript

> [!div renderon="docs"]
> В папке *JavaScriptSPA* измените `index.html` и задайте значения `clientID` и `authority` в разделе `applicationConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> В папке *JavaScriptSPA* измените `index.html` и замените `applicationConfig` следующим:

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
> [!div renderon="docs"]
>
> Описание
> - `Enter_the_Application_Id_here` — это **идентификатор приложения (клиента)**, которое вы зарегистрировали.
> - `Enter_the_Tenant_Info_Here` может иметь несколько значений.
>   - Если приложение поддерживает **учетные записи только в этом каталоге организации**, замените это значение **идентификатором клиента** или **именем клиента** (например, contoso.microsoft.com).
>   - Если ваше приложение поддерживает вариант **Учетные записи в любом каталоге организации**, замените это значение на `organizations`.
>   - Если приложение поддерживает **учетные записи в любом каталоге организации и личные учетные записи Майкрософт**, укажите значение `common`.
>
> > [!TIP]
> > Чтобы найти значения параметров **Идентификатор приложения (клиента)**, **Идентификатор каталога (клиента)** и **Поддерживаемые типы учетных записей**, на портале Azure перейдите на страницу **Обзор**.

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

## <a name="more-information"></a>Дополнительные сведения

### <a name="msaljs"></a>*msal.js*

MSAL — это библиотека, используемая для выполнения входа пользователей и запросов маркеров, используемых для доступа к API, защищенному Microsoft Azure Active Directory (Azure AD). В файле *index.html*, используемом в кратком руководстве, содержится ссылка на библиотеку:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>
```

Кроме того, если у вас установлен Node, можно загрузить его через npm:

```batch
npm install msal
```

### <a name="msal-initialization"></a>Инициализация MSAL

В примере кода ниже показано, как инициализировать библиотеку:

```javascript
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, applicationConfig.authority, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
```

> |Where  |  |
> |---------|---------|
> |`ClientId`     |Идентификатор приложения, зарегистрированного на портале Azure|
> |`authority`    |Это URL-адрес центра. В случае передачи значения *null*, для центра по умолчанию устанавливается значение `https://login.microsoftonline.com/common`. Если вы используете однотенантное приложение (предназначенное для учетных записей только в одном каталоге), установите значение `https://login.microsoftonline.com/<tenant name or ID>`|
> |`tokenReceivedCallback`| Метод обратного вызова, вызываемый, когда механизм аутентификации выполняет перенаправление обратно в приложение. Здесь передается `acquireTokenRedirectCallBack`. При использовании loginPopup он имеет значение NULL.|
> |`options`  |Коллекция необязательных параметров. В этом случае `storeAuthStateInCookie` и `cacheLocation` являются необязательными настройками. Дополнительные сведения о параметрах см. на [этом вики-сайте](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options). |

### <a name="sign-in-users"></a>Выполнение входа пользователей

В приведенном ниже фрагменте кода показано, как выполнить вход пользователей:

```javascript
myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
    //Callback code here
})
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | (Необязательно.) Содержит области, для которых запрашивается согласие пользователя во время входа в систему. Например, `[ "user.read" ]` для Microsoft Graph или `[ "<Application ID URL>/scope" ]` для пользовательских веб-API (то есть `api://<Application ID>/access_as_user`). Здесь передается `applicationConfig.graphScopes`. |

> [!TIP]
> Также можно использовать метод `loginRedirect`, чтобы с текущей страницы выполнялось перенаправление на страницу входа вместо отображения всплывающего окна.

### <a name="request-tokens"></a>Запрос маркеров

В MSAL есть три метода получения маркеров: `acquireTokenRedirect`, `acquireTokenPopup` и `acquireTokenSilent`.

#### <a name="get-a-user-token-silently"></a>Автоматическое получение маркера пользователя

Метод `acquireTokenSilent` обрабатывает получение и обновление маркера без участия пользователя. После первого выполнения метода `loginRedirect` или `loginPopup` обычно используется метод `acquireTokenSilent`, чтобы получить маркеры для доступа к защищенным ресурсам для последующих вызовов. Вызовы для запроса или обновления маркеров выполняются автоматически.

```javascript
myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | Содержит запрашиваемые области, которые должны быть возвращены в маркере доступа для API. Например, `[ "user.read" ]` для Microsoft Graph или `[ "<Application ID URL>/scope" ]` для пользовательских веб-API (то есть `api://<Application ID>/access_as_user`). Здесь передается `applicationConfig.graphScopes`.|

#### <a name="get-a-user-token-interactively"></a>Интерактивное получение маркера пользователя

Иногда требуется настроить принудительное взаимодействие пользователей с конечной точкой Azure AD версии 2.0. Например: 
* Пользователям может потребоваться повторно ввести учетные данные, так как истек срок действия пароля.
* Ваше приложение запрашивает доступ к дополнительным областям ресурса, на обращение к которым пользователь должен дать согласие.
* Требуется двухфакторная проверка подлинности

Обычный рекомендуемый шаблон для большинства приложений — вызов `acquireTokenSilent`, перехват исключения, а затем вызов `acquireTokenRedirect` (или `acquireTokenPopup`) для запуска интерактивного запроса.

После вызова `acquireTokenPopup(scope)` отображается всплывающее окно для входа (или `acquireTokenRedirect(scope)` перенаправляет пользователей на конечную точку Azure AD версии 2.0), где пользователям нужно подтвердить свои учетные данные, предоставить согласие на доступ к требуемому ресурсу или пройти двухфакторную аутентификацию.

```javascript
myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
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
