---
title: Краткое руководство. Использование приложений Android с платформой удостоверений Майкрософт | Azure
description: Узнайте, как приложения Android могут вызывать API, которому требуются маркеры доступа от конечной точки платформы удостоверений Майкрософт.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88a0f16a01a7e421558b5cea99daee6944adae11
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853043"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Краткое руководство. Вход пользователей и вызов API Microsoft Graph из приложения Android

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

В этом кратком руководстве содержится пример кода, который демонстрирует, как приложение Android может входить в личные или рабочие и учебные учетные записи, получать маркер доступа и вызывать API Microsoft Graph.

![Схема работы приложения, создаваемого в этом кратком руководстве](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Предварительные требования**
> * Android Studio 
> * Требуется Android 16+ 


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
> 1. Перейдите на страницу [Регистрация приложений](https://aka.ms/MobileAppReg) Платформы удостоверений Майкрософт для разработчиков.
> 1. Выберите **Новая регистрация**.
> 1. После появления страницы **Регистрация приложения** введите сведения о регистрации приложения:
>      - В разделе **Имя** введите понятное имя приложения, которое будет отображаться пользователям приложения, например `AndroidQuickstart`.
>      - Другие конфигурации на этой странице вы можете пропустить. 
>      - Нажмите кнопку `Register`.
> 1. Щелкните новое приложение и перейдите к `Authentication` > `Add Platform` > `Android`.    
>      - Введите имя пакета из проекта Android Studio. 
>      - Создайте хэш подписи. Инструкции см. на портале.
> 1. Щелкните `Configure` (Настроить) и сохраните JSON-файл ***конфигурации MSAL*** для последующего использования. 

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Шаг 1. Настройка приложения
> Для работы примера кода в этом кратком руководстве необходимо добавить URI перенаправления, совместимые с брокером авторизации. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести это изменение для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-android/green-check.png). Ваше приложение настроено с помощью этих атрибутов

#### <a name="step-2-download-the-project"></a>Шаг 2. Скачивание проекта

* [Скачайте пример кода.](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Шаг 3. Настройка проекта

> [!div renderon="docs"]
> Если вы выбрали вариант 1 выше, можно пропустить эти шаги. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Извлеките и откройте проект в Android Studio.
> 1. В разделе **app** > **src** > **main** > **res** > **raw** откройте **auth_config.json**.
> 1. Отредактируйте **auth_config.json** и замените им JSON-файл с портала Azure. Если вместо этого вы хотите вручную внести изменения:
>    ```javascript
>    {
>       "client_id" : "Enter_the_Application_Id_Here",
>       "authorization_user_agent" : "DEFAULT",
>       "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>       "authorities" : [
>          {
>             "type": "AAD",
>             "audience": {
>                "type": "Enter_the_Audience_Info_Here",
>                "tenant_id": "Enter_the_Tenant_Info_Here"
>             }
>          }
>       ]
>    }
>    ```
> 
> 1. В разделе **app** > **manifests** откройте файл **AndroidManifest.xml**.
> 1. Добавьте показанное ниже действие в узел **manifest\application**. 
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Запустите приложение. 

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > В этом кратком руководстве поддерживается Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> 1. Извлеките и откройте проект в Android Studio.
> 1. В разделе **app** > **res** > **raw** откройте **auth_config.json**.
> 1. Отредактируйте **auth_config.json** и замените им JSON-файл с портала Azure. Если вместо этого вы хотите вручную внести изменения:
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
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Decoded_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Замените `Enter_the_Package_Name` и `Enter_the_Signature_Hash` значениями, зарегистрированными на портале Azure. 
> 1. Запустите приложение. 

## <a name="more-information"></a>Дополнительные сведения

Дополнительные сведения к этому краткому руководству.

### <a name="getting-msal"></a>Получение MSAL

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) — это библиотека, используемая для выполнения входа пользователей и запросов маркеров, которые нужны для доступа к API, защищенному платформой удостоверений Майкрософт. Вы можете использовать Gradle 3.0+ для его установки, добавив следующее в **Gradle Scripts** > **build.gradle (Module: app)** в разделе **Зависимости**.

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.3.+'
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
> |`R.raw.auth_config` | Этот файл содержит конфигурацию вашего приложения, включая идентификатор приложения или клиента, аудиторию входа, URI перенаправления и несколько других параметров настройки. |

### <a name="requesting-tokens"></a>Запрос маркеров

MSAL имеет два метода получения маркеров: `acquireToken` и `acquireTokenSilentAsync`

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken. Интерактивное получение маркера

Иногда требуется взаимодействие пользователей с платформой удостоверений Майкрософт. В таких случаях пользователю может потребоваться выбрать свою учетную запись, ввести учетные данные или предоставить согласие на разрешения, запрошенные приложением. Например, 

* первый вход пользователей в приложение;
* Если пользователь сбрасывает пароль, то потребуется вводить свои учетные данные. 
* Если отменяется согласие. 
* Если приложение явно требует согласия. 
* Когда ваше приложение впервые запрашивает доступ к ресурсу.
* Когда требуются политики условного доступа или MFA.

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Описание||
> |---------|---------|
> | `SCOPES` | Содержит запрашиваемые области (то есть `{ "user.read" }` для Microsoft Graph или `{ "<Application ID URL>/scope" }` для пользовательских веб-API (т. е. `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Обратный вызов выполняется, когда управление возвращается в приложение после проверки подлинности. |

#### <a name="acquiretokensilent-getting-a-user-token-silently"></a>acquireTokenSilent. Автоматическое получение маркера пользователя

Приложения не требуют, чтобы пользователи выполняли вход каждый раз при запросе маркера. Если пользователь уже вошел, этот метод позволяет приложениям запрашивать маркеры автоматически.

```java
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (!accounts.isEmpty()) {
                sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
            } else {
                /* No accounts */
            }
        }
    });
```

> |Описание||
> |---------|---------|
> | `SCOPES` | Содержит запрашиваемые области (то есть `{ "user.read" }` для Microsoft Graph или `{ "<Application ID URL>/scope" }` для пользовательских веб-API (т. е. `api://<Application ID>/access_as_user`) |
> | `getAccounts(...)` | Содержит учетную запись, для которой вы пытаетесь настроить автоматическое получение маркеров |
> | `getAuthSilentCallback()` | Обратный вызов выполняется, когда управление возвращается в приложение после проверки подлинности. |

## <a name="next-steps"></a>Дополнительная информация

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Изучите этапы создания приложения, используемые в этом кратком руководстве

В руководстве по Android вы найдете пошаговые инструкции для создания приложений и функций, а также полное описание того, о чем говорится в этом кратком руководстве.

> [!div class="nextstepaction"]
> [Вход пользователей и вызов API Microsoft Graph из приложения Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>MSAL для вики-сайта библиотеки Android

Дополнительные сведения см. в статье

> [!div class="nextstepaction"]
> [MSAL для вики-сайта библиотеки Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Помогите нам улучшить платформу Microsoft Identity. Поделитесь своим мнением, ответив на два вопроса.

> [!div class="nextstepaction"]
> [Опрос по платформе удостоверений Майкрософт](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)