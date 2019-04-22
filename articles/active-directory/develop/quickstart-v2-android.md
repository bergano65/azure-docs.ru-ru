---
title: Краткое руководство. Использование приложений Android с платформой удостоверений Майкрософт | Azure
description: Узнайте, как приложения Android могут вызывать API, которому требуются маркеры доступа от конечной точки платформы удостоверений Майкрософт.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1f174229da565627c0e5791f53031b338880cb3
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495317"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Краткое руководство. Вход пользователей и вызов API Microsoft Graph из приложения Android

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

В этом кратком руководстве содержится пример кода, который демонстрирует, как приложение Android может входить в личные или рабочие и учебные учетные записи, получать маркер доступа и вызывать API Microsoft Graph.

![Схема работы приложения, создаваемого в этом кратком руководстве](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Предварительные требования**
> * Android Studio 3+
> * Android 21+ 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Регистрация и скачивание приложения, используемого в этом кратком руководстве
> У вас есть два варианта запуска приложения, используемого в этом кратком руководстве:
> * [Экспресс-способ] [Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Вручную] [Вариант 2. Регистрация и настройка приложения и примера кода вручную](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
> Регистрация приложения:
> 1. Откройте на [портале Azure новую панель регистрации приложений](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs).
> 1. Введите имя приложения и нажмите кнопку **Зарегистрировать**.
> 1. Следуйте инструкциям, чтобы быстро скачать и автоматически настроить новое приложение.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Вариант 2. Регистрация и настройка приложения и примера кода вручную
>
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
> Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение вручную, сделайте следующее:
>
> 1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
> 1. Если учетная запись предоставляет доступ нескольким клиентам, выберите свою учетную запись в правом верхнем углу и нужный клиент Azure AD для этого сеанса портала.
> 1. Перейдите на страницу [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) Платформы удостоверений Майкрософт для разработчиков.
> 1. Выберите **Новая регистрация**.
> 1. После появления страницы **Регистрация приложения** введите сведения о регистрации приложения:
>      - В разделе **Имя** введите понятное имя приложения, которое будет отображаться пользователям приложения, например `Android-Quickstart`.
>      - Нажмите кнопку `Register`.
> 1. Поочередно выберите `Authentication` > `Redirect URIs` > `Suggested Redirect URIs for public clients`, а затем выберите URI перенаправления в формате **msal{AppId}://auth**. Сохраните изменения.


> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Шаг 1. Настройка приложения
> Чтобы образец кода для этого краткого руководства работал, вам нужно добавить следующий URL-адрес ответа: **msal {AppId}://auth** (где {AppId} — это идентификатор вашего приложения).
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести это изменение для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-android/green-check.png). Ваше приложение настроено с помощью этих атрибутов.

#### <a name="step-2-download-the-project"></a>Шаг 2. Скачивание проекта

* [Скачайте проект Android Studio](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Шаг 3. Настройка проекта

> [!div renderon="docs"]
> Если вы выбрали вариант 1 выше, можно пропустить эти шаги. Откройте проект в Android Studio и запустите приложение. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Извлеките и откройте проект в Android Studio.
> 1. В разделе **app** > **res** > **raw** откройте **auth_config.json**.
> 1. Отредактируйте **auth_config.json** и замените `client_id` и `tenant_id`:
>    ```javascript
>    "client_id" : "Enter_the_Application_Id_Here",
>    "type": "Enter_the_Audience_Info_Here",
>    "tenant_id" : "Enter_the_Tenant_Info_Here"
>    ```
> 1. В разделе **app** > **manifests** откройте файл **AndroidManifest.xml**.
> 1. Добавьте приведенное ниже действие в узел **manifest\application**. Этот код позволяет Майкрософт выполнять обратный вызов вашего приложения:   
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msalEnter_The_Application_Id_Here"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```

> [!div renderon="docs"]
> 1. Извлеките и откройте проект в Android Studio.
> 1. В разделе **app** > **res** > **raw** откройте **auth_config.json**.
> 1. Отредактируйте **auth_config.json** и замените `client_id` и `redirect_uri`:
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. В разделе **app** > **manifests** откройте файл **AndroidManifest.xml**.
> 1. Добавьте приведенное ниже действие в узел **manifest\application**. Этот фрагмент кода регистрирует **BrowserTabActivity**, чтобы позволить ОС возобновить свое приложение после завершения аутентификации.
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msal<ENTER_YOUR_APPLICATION_ID>"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Замените `<ENTER_THE_APPLICATION_ID_HERE>` *идентификатором приложения* для вашего приложения. Если вам нужно найти *идентификатор приложения*, перейдите на страницу *Обзор*.

## <a name="more-information"></a>Дополнительные сведения

Дополнительные сведения к этому краткому руководству.

### <a name="msal"></a>MSAL

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) — это библиотека, используемая для выполнения входа пользователей и запросов маркеров, которые нужны для доступа к API, защищенному платформой удостоверений Майкрософт. Вы можете использовать Gradle для его установки, добавив следующее в **Gradle Scripts** > **build.gradle (Module: app)** в разделе **Зависимости**.

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.2.+'
```

### <a name="msal-initialization"></a>Инициализация MSAL

Добавив следующий код, вы можете добавить ссылку на MSAL.

```java
import com.microsoft.identity.client.*;
```

Затем выполните инициализацию MSAL с помощью следующего кода.

```java
    sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);
```

> |Описание ||
> |---------|---------|
> |`R.raw.auth_config` | Этот файл содержит конфигурацию вашего приложения, включая идентификатор приложения или клиента, аудиторию входа и несколько других параметров настройки. |

### <a name="requesting-tokens"></a>Запрос маркеров

В MSAL предусмотрены два метода получения маркеров: `acquireToken` и `acquireTokenSilentAsync`

#### <a name="getting-a-user-token-interactively"></a>Интерактивное получение маркера пользователя

Некоторые ситуации требуют принудительного взаимодействия пользователей с конечной точкой платформы удостоверений Майкрософт. Это приводит к переключению контекста на системный браузер для проверки учетных данных пользователей или получения согласия. Некоторые примеры:

* первый вход пользователей в приложение;
* когда пользователям может потребоваться повторно ввести учетные данные, так как истек срок действия пароля;
* когда ваше приложение запрашивает доступ к ресурсу, на обращение к которому пользователь должен дать согласие.
* когда требуется двухфакторная проверка подлинности.

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Описание||
> |---------|---------|
> | `SCOPES` | Содержит запрашиваемые области (то есть `{ "user.read" }` для Microsoft Graph или `{ "<Application ID URL>/scope" }` для пользовательских веб-API (т. е. `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Обратный вызов выполняется, когда управление возвращается в приложение после проверки подлинности. |

#### <a name="getting-a-user-token-silently"></a>Автоматическое получение маркера пользователя

Вы не хотите требовать от пользователя проверки своих учетных данных каждый раз, когда им необходимо получить доступ к ресурсу. Большую часть времени вы хотите приобретать и обновлять маркеры без какого-либо взаимодействия с пользователем. Можно использовать метод `AcquireTokenSilentAsync`, чтобы получить маркеры для доступа к защищенным ресурсам после первоначального метода `acquireToken`.

```java
List<IAccount> accounts = sampleApp.getAccounts();
if (sample.size() == 1) {
    sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
} else {
    // No or multiple accounts
}
```

> |Описание||
> |---------|---------|
> | `SCOPES` | Содержит запрашиваемые области (то есть `{ "user.read" }` для Microsoft Graph или `{ "<Application ID URL>/scope" }` для пользовательских веб-API (т. е. `api://<Application ID>/access_as_user`) |
> | `accounts.get(0)` | Содержит учетную запись, для которой вы пытаетесь настроить автоматическое получение маркеров |
> | `getAuthInteractiveCallback` | Обратный вызов выполняется, когда управление возвращается в приложение после проверки подлинности. |

## <a name="next-steps"></a>Дополнительная информация

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Изучите этапы создания приложения, используемые в этом кратком руководстве

В руководстве по Android вы найдете пошаговые инструкции для создания приложений и функций, а также полное описание того, о чем говорится в этом кратком руководстве.

> [!div class="nextstepaction"]
> [Руководство по вызову API Graph из приложения Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>MSAL для вики-сайта библиотеки Android

Дополнительные сведения см. в статье

> [!div class="nextstepaction"]
> [MSAL для вики-сайта библиотеки Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
