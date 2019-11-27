---
title: Использование универсальных кодов ресурсов (URI) перенаправления с помощью библиотеки аутентификации Майкрософт
titleSuffix: Microsoft identity platform
description: Из этой статьи вы узнаете о различиях между библиотекой аутентификации Майкрософт для ObjectiveC (MSAL для iOS и macOS) и библиотекой аутентификации Azure AD для ObjectiveC (ADAL.ObjC) и о том, как выполнить перенос между ними.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: jak
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b78906a03eb9dc96fb58dea4ceabff953f2a3e4f
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803788"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Использование URI перенаправления с помощью библиотеки аутентификации Майкрософт для iOS и macOS

Когда пользователь проходит аутентификацию, Azure Active Directory (Azure AD) отправляет маркер в приложение с помощью URI перенаправления, зарегистрированного в приложении Azure AD.

Для библиотеки аутентификации Майкрософт (MSAL) требуется, чтобы URI перенаправления был зарегистрирован в приложении Azure AD в определенном формате. MSAL использует URI перенаправления по умолчанию, если он не указан. Формат — `msauth.[Your_Bundle_Id]://auth`.

Формат URI перенаправления по умолчанию работает для большинства приложений и сценариев, включая аутентификацию через посредника и веб-представление системы. При возможности используйте формат по умолчанию.

Однако для расширенных сценариев может потребоваться изменить URI перенаправления, как описано ниже.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Сценарии, для которых требуются другие URI перенаправления

### <a name="cross-app-single-sign-on-sso"></a>Единый вход между приложениями (SSO)

Чтобы платформа удостоверений Майкрософт совместно использовала маркеры между приложениями, каждое приложение должно иметь одинаковый идентификатор клиента или идентификатор приложения. Это уникальный идентификатор, предоставляемый при регистрации приложения на портале (а не идентификатор пакета приложения, регистрируемый для каждого приложения в Apple).

URI перенаправления должны быть разными для каждого приложения iOS. Это позволяет службе идентификации Майкрософт однозначно определять различные приложения, которые совместно используют идентификатор приложения. Каждое приложение может иметь несколько URI перенаправления, зарегистрированных на портале Azure. Каждое приложение в наборе получит уникальный код URI перенаправления. Например:

С учетом следующей регистрации приложения в портал Azure:

    Client ID: ABCDE-12345 (this is a single client ID)
    RedirectUris: msauth.com.contoso.app1://auth, msauth.com.contoso.app2://auth, msauth.com.contoso.app3://auth

Приложение1 использует перенаправление `msauth.com.contoso.app1://auth`, Приложение2 использует `msauth.com.contoso.app2://auth` Приложение3 использует `msauth.com.contoso.app1://auth`

### <a name="migrating-from-adal-to-msal"></a>Миграция с ADAL к MSAL

При переносе кода, использующего библиотеку аутентификации Azure AD (ADAL), в MSAL, для приложения уже может быть настроен URI перенаправления. Вы можете продолжать использовать тот же URI перенаправления, если ваше приложение ADAL было настроено для поддержки сценариев с брокером, а ваш URI перенаправления соответствует требованиям формата URI перенаправления MSAL.

## <a name="msal-redirect-uri-format-requirements"></a>Требования к формату URI перенаправления MSAL

* URI перенаправления MSAL должен иметь форму `<scheme>://host`,

    где `<scheme>` — уникальная строка, идентифицирующая ваше приложение. Она основана главным образом на идентификаторе пакета приложения, чтобы гарантировать уникальность. Например, если идентификатор пакета приложения равен `com.contoso.myapp`, URI перенаправления будет иметь форму: `msauth.com.contoso.myapp://auth`.

    Если вы выполняете перенос с ADAL, URI перенаправления, вероятно, будет иметь следующий формат: `<scheme>://[Your_Bundle_Id]`, где `scheme` — уникальная строка. Этот формат будет продолжать работать при использовании MSAL.

* `<scheme>` необходимо зарегистрировать в файле "Info.plist" приложения в разделе `CFBundleURLTypes > CFBundleURLSchemes`.  В этом примере файл "Info.plist" был открыт как исходный код:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>msauth.[BUNDLE_ID]</string>
            </array>
        </dict>
    </array>
    ```
    

MSAL проверит, правильно ли регистрируется URI перенаправления, и в возвратном отчете отобразит ошибку, если это не так.
    
* Если вы хотите использовать универсальные ссылки в качестве URI перенаправления, `<scheme>` должно быть `https` и не должно быть объявлено в `CFBundleURLSchemes`. Вместо этого настройте приложение и домен в соответствии с инструкциями Apple [Универсальные ссылки для разработчиков](https://developer.apple.com/ios/universal-links/) и вызовите метод `handleMSALResponse:sourceApplication:` `MSALPublicClientApplication`, когда приложение открывается через универсальную ссылку.

## <a name="use-a-custom-redirect-uri"></a>Использование пользовательского URI перенаправления

Чтобы использовать пользовательский URI перенаправления, передайте параметр `redirectUri` в `MSALPublicClientApplicationConfig` и передайте этот объект в `MSALPublicClientApplication` при инициализации объекта. Если URI перенаправления недействителен, инициализатор вернет `nil` и настроит `redirectURIError`, используя дополнительную информацию.  Например:

Objective-C.

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>Обрабатывает событие, открытое URL-адресом

Приложение должно вызывать MSAL при получении любого ответа через схемы URL-адресов или универсальные ссылки. Вызовите метод `handleMSALResponse:sourceApplication:` для `MSALPublicClientApplication`, когда приложение открыто. Ниже приведен пример пользовательских схем.

Objective-C.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в [Потоки проверки подлинности и сценарии приложений](authentication-flows-app-scenarios.md)
