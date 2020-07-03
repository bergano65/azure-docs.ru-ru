---
title: Вход пользователей в одностраничных приложениях JavaScript с помощью кода проверки подлинности | Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как приложение JavaScript может вызывать API, которому необходимы маркеры доступа, используя платформу удостоверений Майкрософт.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ROBOTS: NOINDEX
ms.openlocfilehash: 9663c11508b0478a67f528cb301d705a3125e4f6
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871522"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>Краткое руководство. Вход пользователей и получение маркера доступа в SPA JavaScript с помощью потока кода авторизации 

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.


В этом кратком руководстве используется MSAL.js версии 2.0 с потоком кода авторизации. Чтобы использовать MSAL.js версии 1.0 с неявным потоком, просмотрите [это краткое руководство](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript).

В этом кратком руководстве описано, как с помощью примера кода можно узнать, как одностраничное приложение (SPA) JavaScript может выполнять вход с помощью личных, рабочих и учебных учетных записей. Одностраничное приложение JavaScript также может получить маркер доступа для вызова API Microsoft Graph или любого веб-API. Иллюстрацию см. в разделе [Как работает этот пример](#how-the-sample-works).

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. [Создать подписку Azure бесплатно](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (для внесения правок в файлы проекта).


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Регистрация и скачивание приложения, используемого в этом кратком руководстве
> Чтобы запустить приложение, используемое в этом кратком руководстве, выберите любой из следующих вариантов.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Вариант 1 (экспресс-способ). Регистрация и автоматическая настройка приложения, а затем скачивание примера кода
>
> 1. Войдите на [портал Azure](https://portal.azure.com).
> 1. Если ваша учетная запись предоставляет доступ к нескольким клиентам, в правом верхнем углу щелкните свою учетную запись и выберите для текущего сеанса работы нужный клиент Azure AD.
> 1. Щелкните [Регистрация приложений](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Введите имя приложения.
> 1. В разделе **Поддерживаемые типы учетных записей** выберите **Accounts in any organizational directory and personal Microsoft accounts** (Учетные записи в любом каталоге организации и личные учетные записи Майкрософт).
> 1. Выберите **Зарегистрировать**.
> 1. Перейдите на панель быстрого запуска и следуйте инструкциям по скачиванию и автоматической настройке нового приложения.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Вариант 2 (вручную). Регистрация и настройка приложения и примера кода вручную
>
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
>
> 1. Войдите на [портал Azure](https://portal.azure.com).
>
> 1. Если ваша учетная запись предоставляет доступ к нескольким клиентам, в правом верхнем углу щелкните свою учетную запись и выберите для текущего сеанса работы нужный клиент Azure AD.
> 1. Щелкните [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908).
> 1. Выберите **Новая регистрация**.
> 1. Когда откроется страница **Register an application** (Регистрация приложения), введите имя приложения.
> 1. В разделе **Поддерживаемые типы учетных записей** выберите **Accounts in any organizational directory and personal Microsoft accounts** (Учетные записи в любом каталоге организации и личные учетные записи Майкрософт).
> 1. Выберите **Зарегистрировать**. На странице приложения **Обзор** запишите **идентификатор приложения (клиента)** для использования в будущем.
> 1. В левой области зарегистрированного приложения выберите **Проверка подлинности**.
> 1. В разделе **Конфигурации платформ** щелкните **Добавить платформу**. Откроется панель слева. Выберите область **Одностраничные приложения**.
> 1. В этой же левой части задайте для **URI перенаправления** значение `http://localhost:3000/`. 
> 1. Нажмите кнопку **Настроить**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Шаг 1. Настройка приложения на портале Azure
> Для работы примера кода в этом кратком руководстве необходимо добавить `redirectUri` со значением `http://localhost:3000/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести эти изменения для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-javascript/green-check.png). Ваше приложение настроено с помощью этих атрибутов.

#### <a name="step-2-download-the-project"></a>Шаг 2. Скачивание проекта

> [!div renderon="docs"]
> [Скачайте основные файлы проекта](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip), чтобы запустить проект с помощью веб-сервера, используя Node.js.

> [!div renderon="portal" class="sxs-lookup"]
> Запустите проект с помощью веб-сервера, используя Node.js.

> [!div renderon="portal" id="autoupdate" class="nextstepaction" class="sxs-lookup"]
> [Скачивание примера кода](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Шаг 3. Настройка приложения JavaScript
>
> В папке *приложения* измените файл *authConfig.js* и установите значения `clientID`, `authority` и `redirectUri` в разделе `msalConfig`.
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > :::no-loc text="Enter_the_Supported_Account_Info_Here":::

> [!div renderon="docs"]
>
> Где:
> - *\<Enter_the_Application_Id_Here>*  — **Идентификатор приложения (клиент)** для зарегистрированного приложения.
> - *\<Enter_the_Cloud_Instance_Id_Here>*  — экземпляр облака Azure. Для основного или глобального облака Azure просто введите *https://login.microsoftonline.com/* . Сведения для **национальных** облаков (например, Китая) см. в [этой статье](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
> - *\<Enter_the_Tenant_info_here>* присваивается одно из следующих значений:
>    - Если приложение поддерживает *учетные записи только в этом каталоге организации*, замените это значение **идентификатором клиента** или **именем клиента** (например, *contoso.microsoft.com*).
>    - Если ваше приложение поддерживает *учетные записи в любом каталоге организации*, замените это значение на **organizations**.
>    - Если приложение поддерживает *учетные записи в любом каталоге организации и личные учетные записи Майкрософт*, замените это значение на **common**. Чтобы ограничить поддержку только *личными учетными записями Майкрософт*, замените это значение на **consumers**.
> - *\<Enter_the_Redirect_Uri_Here>* имеет значение `http://localhost:3000`
> > [!TIP]
> > Чтобы найти значения параметров **Идентификатор приложения (клиента)** , **Идентификатор каталога (клиента)** и **Поддерживаемые типы учетных записей**, на портале Azure перейдите на страницу регистрации приложения **Обзор**.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Шаг 3. Приложение настроено и готово к запуску
> Мы настроили проект, указав значения свойств приложения.

> [!div renderon="docs"]
>
> Затем в той же папке измените файл *graphConfig.js*, задав значения `graphMeEndpoint` и `graphMailEndpoint` для объекта `apiConfig`.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> *\<Enter_the_Graph_Endpoint_Here>*  — это конечная точка, для которой будут выполняться вызовы API. Для основной или глобальной службы API Microsoft Graph введите `https://graph.microsoft.com`. Дополнительные сведения см. в статье [Национальные облачные развертывания](https://docs.microsoft.com/graph/deployments).
>
> #### <a name="step-4-run-the-project"></a>Шаг 4. Запуск проекта

Запустите проект с помощью веб-сервера, используя [Node.js](https://nodejs.org/en/download/):

1. Чтобы запустить сервер, выполните в каталоге проекта следующую команду.
    ```bash
    npm install
    npm start
    ```
1. Перейдите по адресу `http://localhost:3000/`.

1. Нажмите кнопку **Войти**, чтобы начать процесс входа в систему, а затем вызовите API Microsoft Graph.

    После первого входа предоставьте приложению разрешение на использование данных вашего профиля для входа. После выполнения входа сведения о вашем профиле пользователя должны отображаться на странице.

## <a name="more-information"></a>Дополнительные сведения

### <a name="how-the-sample-works"></a>Как работает этот пример

![Принцип работы примера JavaScript SPA: 1. SPA инициирует вход. 2. SPA получает токен идентификатора от платформы удостоверений Майкрософт. 3. SPA вызывает получение токена. 4. Платформа удостоверений Майкрософт возвращает токен доступа в SPA. 5. SPA выполняет HTTP-запрос GET с маркером доступа к Microsoft API Graph. 6. API Graph возвращает ответ HTTP в SPA.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

MSAL.js — это библиотека, используемая для выполнения входа пользователей и запросов маркеров, которые нужны для доступа к API, защищенному платформой удостоверений Майкрософт. В файле примера *index.html* содержится ссылка на библиотеку:

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```
> [!TIP]
> Приведенную выше версию можно заменить последней выпущенной версией в разделе с [выпусками MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Кроме того, если вы установили Node.js, последнюю версию можно скачать с помощью диспетчера пакетов Node.js (NPM):

```batch
npm install @azure/msal-browser
```

## <a name="next-steps"></a>Дальнейшие действия

[Репозиторий GitHub MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js) содержит дополнительную документацию по библиотеке, часто задаваемые вопросы и предоставляет поддержку по устранению неполадок.

Более подробное пошаговое руководство по созданию приложения для этого краткого руководства см. в следующей статье:

> [!div class="nextstepaction"]
> [Tutorial to sign in and call MS Graph](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code) (Руководство по вызову API Microsoft Graph)
