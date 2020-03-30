---
title: Настройка SSO на macOS и iOS
titleSuffix: Microsoft identity platform
description: Узнайте, как настроить один знак на (SSO) на macOS и iOS.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 91a55520b37c549c8f1d94ba6cf08ecd24db85b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262455"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Как: Налаживание SSO на macOS и iOS

Библиотека аутентификации Microsoft (MSAL) для macOS и iOS поддерживает единый входе (SSO) между приложениями macOS/iOS и браузерами. В этой статье рассматриваются следующие сценарии SSO:

- [Silent SSO между несколькими приложениями](#silent-sso-between-apps)

Этот тип SSO работает между несколькими приложениями, распространяемыми одним и тем же разработчиком Apple. Он обеспечивает молчание SSO (то есть, пользователь не побудило для учетных данных) читая маркеры обновления, написанные другими приложениями из брелока, и обменивая их для доступа к токенам молча.  

- [SSO через брокера аутентификации](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Этот поток недоступен на macOS.

Корпорация Майкрософт предоставляет приложения, называемые брокерами, которые позволяют SSO между приложениями от разных поставщиков до тех пор, пока мобильное устройство зарегистрировано в Azure Active Directory (AAD). Этот тип SSO требует, чтобы приложение брокера было установлено на устройстве пользователя.

- **SSO между MSAL и Safari**

SSO достигается через класс [ASWebAuthenticationationSession.](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) Он использует существующее состояние вхаса из других приложений и браузера Safari. Он не ограничивается приложениями, распространяемыми тем же разработчиком Apple, но требует некоторого взаимодействия с пользователем.

Если вы используете веб-представление по умолчанию в приложении для регистрации пользователей, вы получите автоматический SSO между приложениями на основе MSAL и Safari. Чтобы узнать больше о веб-представлениях, которые поддерживает MSAL, посетите [настраиваемые браузеры и WebViews.](customize-webviews.md)

> [!IMPORTANT]
> Этот тип SSO в настоящее время недоступен на macOS. MSAL на macOS поддерживает только WKWebView, который не имеет поддержки SSO с Safari. 

- **Silent SSO между приложениями ADAL и MSAL macOS/iOS**

MSAL Objective-C поддерживает миграцию и SSO с помощью приложений на основе ADAL Objective-C. Приложения должны распространяться тем же разработчиком Apple.

Смотрите [SSO между приложениями ADAL и MSAL на macOS и iOS](sso-between-adal-msal-apps-macos-ios.md) для инструкций для кросс-приложений SSO между приложениями ADAL и MSAL.

## <a name="silent-sso-between-apps"></a>Silent SSO между приложениями

MSAL поддерживает совместное использование SSO через группы доступа к брелоку iOS.

Для включения SSO в приложения необходимо сделать следующие шаги, которые описаны более подробно ниже:

1. Убедитесь, что все ваши приложения используют один и тот же идентификатор клиента или идентификатор приложения.
1. Убедитесь, что все приложения имеют один и тот же самозаверяющий сертификат от Apple для общего доступа к цепочкам ключей.
1. Запросите одинаковое назначение цепочки ключей для каждого из приложений.
1. Сообщите MSAL SDKs об общей брелоке, который вы хотите использовать, если она отличается от по умолчанию.

### <a name="use-the-same-client-id-and-application-id"></a>Используйте тот же идентификатор клиента и идентификатор приложения

Для того чтобы платформа майкрософт знать, какие приложения могут обмениваться токенами, эти приложения должны совместно с тем же идентификатором клиента или идентификатором приложения. Это уникальный идентификатор, предоставленный вам при регистрации первого приложения на портале.

То, как платформа идентификации Майкрософт сообщает приложениям, которые используют тот же идентификатор приложения друг от друга, является их **перенаправление URIs.** Каждое приложение может иметь несколько кодов URI перенаправления, зарегистрированных на портале подключения. Каждое приложение в наборе получит уникальный код URI перенаправления. Пример:

URI перенаправления App1: `msauth.com.contoso.mytestapp1://auth`  
URI перенаправления App2: `msauth.com.contoso.mytestapp2://auth`  
URI перенаправления App3: `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> Формат перенаправления uris должен быть совместим с форматом поддержки MSAL, который задокументирован в [требованиях формата MSAL Redirect URI.](redirect-uris-ios.md#msal-redirect-uri-format-requirements)

### <a name="setup-keychain-sharing-between-applications"></a>Настройка совместного использования брелока между приложениями

Обратитесь к статье Apple [«Добавление возможностей»](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) для обеспечения совместного использования брелока. Важно то, что вы решаете, что вы хотите, чтобы ваш брелок будет называться и добавить эту возможность для всех ваших приложений, которые будут участвовать в SSO.

Если у вас есть права настроены правильно, `entitlements.plist` вы увидите файл в каталоге проекта, который содержит что-то вроде этого примера:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

#### <a name="add-a-new-keychain-group"></a>Добавление новой группы брелоков

Добавьте новую группу брелоков в свой проект **Capabilities.** Группа брелоков должна быть:
* `com.microsoft.adalcache`на iOS 
* `com.microsoft.identity.universalstorage`на macOS.

![пример брелока](media/single-sign-on-macos-ios/keychain-example.png)

Для получения дополнительной [keychain groups](howto-v2-keychain-objc.md)информации см.

## <a name="configure-the-application-object"></a>Настройка объекта приложения

Если у вас есть право на брелок в каждом из ваших приложений, `MSALPublicClientApplication` и вы готовы использовать SSO, настроить с вашей группы доступа брелока, как в следующем примере:

Objective-C.

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"

do {
   let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
} catch let error as NSError {
  // handle error here
}
```

> [!WARNING]
> Когда вы делитесь брелоком в приложениях, любое приложение может удалить пользователей или даже все маркеры в приложении.
> Это особенно важно, если у вас есть приложения, которые полагаются на токены для работы с фоновыми сведениями.
> Совместное использование брелока означает, что вы должны быть очень осторожны, когда ваше приложение использует Microsoft identity SDK удалить операции.

Вот и все! Теперь SDK, удостоверяющий личность Майкрософт, будет обмениваться учетными данными во всех приложениях. Список учетных записей также будет распространен в экземплярах приложений.

## <a name="sso-through-authentication-broker-on-ios"></a>SSO через брокера аутентификации на iOS

MSAL предоставляет поддержку при посредничестве аутентификации с помощью Microsoft Authenticator. Microsoft Authenticator предоставляет SSO для зарегистрированных устройств AAD, а также помогает приложению следовать политикам условного доступа.

Ниже приведены следующие шаги: как вы включите SSO с помощью брокера аутентификации для вашего приложения:

1. Зарегистрируйте совместимый с брокером формат Redirect URI для приложения в Info.plist вашего приложения. Брокер совместимый формат Redirect `msauth.<app.bundle.id>://auth`URI . Замените идентификатор пакета<app.bundle.id>'. Пример:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Добавьте следующие схемы в Info.plist вашего приложения под: `LSApplicationQueriesSchemes`

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Добавьте следующее `AppDelegate.m` в файл для обработки обратных вызовов:

    Objective-C.
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Если вы используете Xcode 11,** вы должны `SceneDelegate` поместить MSAL обратный вызов в файл вместо.
Если UISceneDelegate и UIApplicationDelegate поддерживаются для совместимости с более старыми версиями iOS, то обратный вызов MSAL потребуется поместить в оба файла.

Objective-C.

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в [Потоки проверки подлинности и сценарии приложений](authentication-flows-app-scenarios.md)