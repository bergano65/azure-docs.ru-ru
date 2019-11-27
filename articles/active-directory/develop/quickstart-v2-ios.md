---
title: Краткое руководство. Использование приложений iOS и macOS с платформой удостоверений Майкрософт | Azure
description: Узнайте, как осуществлять вход пользователям и запрашивать Microsoft Graph в приложении iOS или macOS.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2019
ms.author: twhitney
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.collection: M365-identity-device-management
ms.openlocfilehash: b515a7954d82cdd377cec72fa8525fbd9691351d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149511"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Краткое руководство. Вход пользователей и вызов Microsoft API Graph из приложения iOS или macOS

В этом кратком руководстве содержится пример кода, который демонстрирует, как собственное приложение iOS или macOS может использовать платформу удостоверений Майкрософт, чтобы входить в личные, рабочие и школьные учетные записи, получать маркер доступа и вызывать API Microsoft Graph.

Это краткое руководство распространяется на приложения iOS и macOS. Некоторые шаги необходимы только для приложений iOS. Эти действия обращают внимание на то, что они предназначены только для iOS.

![Схема работы приложения, создаваемого в этом кратком руководстве](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Предварительные требования**
> * XCode 10 или более поздней версии
> * iOS 10 или более поздней версии 
> * macOS 10.12+

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Регистрация и скачивание приложения, используемого в этом кратком руководстве
> У вас есть два варианта запуска приложения, используемого в этом кратком руководстве:
> * [Экспресс-способ] [Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Вручную] [Вариант 2. Регистрация и настройка приложения и примера кода вручную](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода.
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
> Регистрация приложения:
> 1. Откройте на [портале Azure новую панель регистрации приложений](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs).
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
>      - В разделе **Имя** введите понятное имя приложения, которое будет отображаться пользователям, когда они входят в приложение и предоставляют ему разрешения.
>      - Другие конфигурации на этой странице можно пропустить.
>      - Выберите `Register`.
> 1. В разделе **Управление** выберите `Authentication` > `Add Platform` > `iOS`.
>      - Введите ***идентификатор пакета*** вашего приложения. Идентификатор пакета — это просто уникальная строка, однозначно идентифицирующая ваше приложение, например `com.<yourname>.identitysample.MSALMacOS`. Запишите значение, которое используете.
>      - Обратите внимание, что конфигурация iOS также применима к приложениям macOS.
> 1. Щелкните `Configure` (Настроить) и сохраните сведения о ***конфигурации MSAL*** для последующего использования в этом кратком руководстве.
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Шаг 1. Настройка приложения
> Для работы примера кода в этом кратком руководстве необходимо добавить URI перенаправления, совместимые с брокером авторизации.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести это изменение для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-ios/green-check.png). Ваше приложение настроено с помощью этих атрибутов

#### <a name="step-2-download-the-sample-project"></a>Шаг 2. Загрузка примера проекта

- [Скачивание примера кода для iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Скачивание примера кода для macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Шаг 3. Установка зависимостей

В окне терминала перейдите к папке со скачанным примером кода и выполните `pod install`, чтобы установить последнюю библиотеку MSAL.

#### <a name="step-4-configure-your-project"></a>Шаг 4. Настройка проекта

> [!div renderon="docs"]
> Если вы выбрали вариант 1 выше, можно пропустить эти шаги. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Извлеките ZIP-файл и откройте проект в XCode.
> 1. Измените **ViewController.swift** и замените строку, начинающуюся с "let kClientID", следующим фрагментом кода. Не забудьте обновить значение `kClientID`, указав идентификатор клиента, который вы ранее сохранили при регистрации приложения на портале в этом кратком руководстве.
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Откройте параметры проекта. В разделе **Идентификатор** введите **идентификатор пакета**, введенный на портале.
> 1. (Только для iOS) Щелкните **Info.plist**, а затем выберите **Открыть как** > **Исходный код**.
> 1. (Только для iOS) В корневом узле словаря замените `CFBundleURLSchemes` на ***идентификатор пакета***, введенный на портале.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>    ```
> 1. Создайте и запустите приложение.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > В этом кратком руководстве поддерживается Enter_the_Supported_Account_Info_Here.
> [!div renderon="docs"]
>
> 1. Извлеките ZIP-файл и откройте проект в XCode.
> 1. Измените **ViewController.swift** и замените строку, начинающуюся с "let kClientID", следующим фрагментом кода. Не забудьте обновить значение `kClientID`, указав идентификатор клиента, который вы ранее сохранили при регистрации приложения на портале в этом кратком руководстве:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Откройте параметры проекта. В разделе **Идентификатор** введите **идентификатор пакета**, введенный на портале.
> 1. (Только для iOS) Щелкните **Info.plist**, а затем выберите **Открыть как** > **Исходный код**.
> 1. (Только для iOS) В корневом узле словаря замените `Enter_the_bundle_Id_Here` на ***идентификатор пакета***, использованный на портале.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
> 
>    ```
> 1. Создайте и запустите приложение. 

## <a name="more-information"></a>Дополнительные сведения

Дополнительные сведения о кратком руководстве содержатся в этих разделах.

### <a name="get-msal"></a>Получение MSAL

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) — это библиотека, используемая для выполнения входа пользователей и запросов маркеров, которые нужны для доступа к API, защищенному платформой удостоверений Майкрософт. MSAL можно добавить в приложение, используя следующее действие.

```
$ vi Podfile

```
Добавьте в файл Podfile следующий код (целевую исполняющую среду проекта):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

Выполните команду установки CocoaPods:

```pod install```

### <a name="initialize-msal"></a>Инициализация MSAL

Добавив следующий код, вы можете добавить ссылку на MSAL.

```swift
import MSAL
```

Затем выполните инициализацию MSAL с помощью следующего кода.

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)
            
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |Описание ||
> |---------|---------|
> | `clientId` | Идентификатор зарегистрированного приложения на *portal.azure.com* |
> | `authority` | Конечная точка платформы удостоверений Майкрософт. В большинстве случаев это будет *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | URI перенаправления приложения. Можете передать нуль, чтобы использовать значение по умолчанию, или ваш пользовательский URI перенаправления. |

### <a name="for-ios-only-additional-app-requirements"></a>(Только для iOS) Дополнительные требования для приложения

Ваше приложение также должно содержать следующий код в объекте `AppDelegate`. Это позволит пакету SDK MSAL обрабатывать ответ токена из приложения брокера авторизации при выполнении проверки подлинности.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> (В iOS 13+) Если вы принимаете `UISceneDelegate` вместо `UIApplicationDelegate`, поместите этот код в обратный вызов `scene:openURLContexts:` (см. [Документацию Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)).
> Если UISceneDelegate и UIApplicationDelegate поддерживаются для совместимости с более старыми версиями iOS, то обратный вызов MSAL потребуется поместить в оба места.

 ```swift
 func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
 ```

Наконец, ваше приложение должно содержать запись `LSApplicationQueriesSchemes` в файле ***Info.plist*** вместе с элементом `CFBundleURLTypes`. Пример уже содержит эту запись. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Выполнение входа пользователей и запрос токенов

MSAL имеет два метода получения маркеров безопасности: `acquireToken` и `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken. Интерактивное получение маркера

Иногда требуется взаимодействие пользователей с платформой удостоверений Майкрософт. В таких случаях пользователю может потребоваться выбрать свою учетную запись, ввести учетные данные или предоставить согласие на разрешения приложения. Например, 

* первый вход пользователей в приложение;
* Если пользователь сбрасывает пароль, то потребуется вводить свои учетные данные. 
* Когда ваше приложение впервые запрашивает доступ к ресурсу.
* Когда требуются политики условного доступа или MFA.

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Описание||
> |---------|---------|
> | `scopes` | Содержит запрашиваемые области (то есть `[ "user.read" ]` для Microsoft Graph или `[ "<Application ID URL>/scope" ]` для пользовательских веб-API (`api://<Application ID>/access_as_user`)). |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent. Автоматическое получение маркера доступа

Приложения не требуют, чтобы пользователи выполняли вход каждый раз при запросе маркера. Если пользователь уже вошел, этот метод позволяет приложениям запрашивать маркеры автоматически. 

```swift
guard let account = try self.applicationContext!.allAccounts().first else { return }
        
let silentParams = MSALSilentTokenParameters(scopes: kScopes, account: account)
self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
```

> |Описание ||
> |---------|---------|
> | `scopes` | Содержит запрашиваемые области (то есть `[ "user.read" ]` для Microsoft Graph или `[ "<Application ID URL>/scope" ]` для пользовательских веб-API (`api://<Application ID>/access_as_user`)). |
> | `account` | Учетная запись, для которой запрашивается токен. В этом кратком руководстве рассматривается приложение с одной учетной записью. Если вы хотите создать приложение с несколькими учетными записями, нужно определить логику для выбора учетной записи, которая будет использоваться для запросов токенов, с помощью `applicationContext.account(forHomeAccountId: self.homeAccountId)`. |

## <a name="next-steps"></a>Дополнительная информация

В руководстве по iOS вы найдете пошаговые инструкции по созданию приложений, а также полное описание того, о чем говорится в этом кратком руководстве.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>Узнайте, как создавать приложения, используемые в этом кратком руководстве.

> [!div class="nextstepaction"]
> [Вызов API Microsoft Graph из приложения iOS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Помогите нам улучшить платформу Microsoft Identity. Поделитесь своим мнением, ответив на два вопроса.

> [!div class="nextstepaction"]
> [Опрос по платформе удостоверений Майкрософт](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
