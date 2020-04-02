---
title: Настройка браузеров & WebViews (MSAL iOS/macOS) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как настроить интерфейс браузера MSAL iOS/macOS для регистрации пользователей.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 8552fc8555207c5b6ca59bbd0da0fdebaae2e87b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546106"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Как настроить браузеры и веб-просмотры для iOS/macOS

Для интерактивной аутентификации необходим веб-браузер. На iOS и macOS 10.15 библиотека подлинности Майкрософт (MSAL) использует веб-браузер системы по умолчанию (который может появиться в верхней части приложения) для интерактивной аутентификации для регистрации пользователей. Преимущество системного браузера — совместное использование состояния Единого знака (SSO) с другими приложениями и с веб-приложениями.

Вы можете изменить опыт, настроив конфигурацию на другие варианты отображения веб-контента, такие как:

Только для iOS:

- [SFAuthenticationСессия](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

Для iOS и macOS:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MSAL для macOS `WKWebView` поддерживает только на старых версиях ОС. `ASWebAuthenticationSession`поддерживается только на macOS 10.15 и выше. 

## <a name="system-browsers"></a>Системные браузеры

Для `ASWebAuthenticationSession`iOS, `SFAuthenticationSession`, `SFSafariViewController` и считаются системными браузерами. Для macOS `ASWebAuthenticationSession` доступен только. Как правило, системные браузеры обмениваются файлами cookie и другими данными веб-сайта с помощью браузерного приложения Safari.

По умолчанию MSAL динамически обнаружит версию iOS и выберет рекомендуемый системный браузер, доступный на этой версии. На iOS 12 "это будет `ASWebAuthenticationSession`. 

### <a name="default-configuration-for-ios"></a>Конфигурация по умолчанию для iOS

| Версия | браузер |
|:-------------:|:-------------:|
| iOS 12 | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationСессия |
| iOS 10 | SFSafariViewController |

### <a name="default-configuration-for-macos"></a>Конфигурация по умолчанию для macOS

| Версия | браузер |
|:-------------:|:-------------:|
| macOS 10.15 | ASWebAuthenticationSession |
| другие версии | WKWebView |

Разработчики также могут выбрать другой системный браузер для приложений MSAL:

- `SFAuthenticationSession`является версия iOS 11 `ASWebAuthenticationSession`.
- `SFSafariViewController`является более общей целью и обеспечивает интерфейс для просмотра веб-страниц и может быть использован для входа целей, а также. В iOS 9 и 10 файлы cookie и другие данные веб-сайта передаются Safari, но не в iOS 11 и позже.

## <a name="in-app-browser"></a>Браузер в приложении

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) — это браузер в приложении, который отображает веб-контент. Он не делится файлами cookie или данными веб-сайта с другими экземплярами **WKWebView** или с браузером Safari. WKWebView — это кросс-платформенный браузер, доступный как для iOS, так и для macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Совместное использование файлов cookie и последствия единого ввоза (SSO)

Браузер, который вы используете, влияет на опыт SSO из-за того, как они делятся файлами cookie. Следующие таблицы обобщаются sSO опытом в браузере.

| Технология    | Тип браузера  | Доступность iOS | доступность macOS | Обмен файлами cookie и другими данными  | Доступность MSAL | Единый вход |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Система | iOS12 и вверх | macOS 10.15 и выше | Да | iOS и macOS 10.15 | w/ Экземпляры Safari
| [SFAuthenticationСессия](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Система | iOS11 и вверх | Н/Д | Да | Только iOS |  w/ Экземпляры Safari
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Система | iOS11 и вверх | Н/Д | нет | Только iOS | Нет, нет.
| **SFSafariViewController** | Система | iOS10 | Н/Д | Да | Только iOS |  w/ Экземпляры Safari
| **WKWebView**  | В приложении | iOS8 и вверх | macOS 10.10 и выше | нет | iOS и macOS | Нет, нет.

Для работы SSO токены должны быть разделены между приложениями. Для этого требуется кэш маркеров или приложение брокера, например Microsoft Authenticator для iOS.

## <a name="change-the-default-browser-for-the-request"></a>Изменение браузера по умолчанию для запроса

Вы можете использовать браузер в приложении или определенный системный браузер в зависимости `MSALWebviewParameters`от ваших требований к UX, изменив следующее свойство в:

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Изменение в интерактивном запросе

Каждый запрос может быть настроен для переопределения `MSALInteractiveTokenParameters.webviewParameters.webviewType` браузера по `acquireTokenWithParameters:completionBlock:` умолчанию путем изменения свойства перед передачей в API.

Кроме того, MSAL поддерживает `WKWebView` прохождение `MSALInteractiveTokenParameters.webviewParameters.customWebView` в обычай, установив свойство.

Пример:

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithParentViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(parentViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

При использовании пользовательского веб-взрения используются уведомления для обозначения состояния отображаемого веб-контента, например:

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>Параметры

Все типы веб-браузеров, поддерживаемые MSAL, заявлены в [enum MSALWebviewType](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
    /**
     For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession or SFAuthenticationSession).
     For older versions, with AuthenticationSession not being available, uses SafariViewController.
     For macOS 10.15 and above uses ASWebAuthenticationSession
     For older macOS versions uses WKWebView
     */
    MSALWebviewTypeDefault,
    
    /** Use ASWebAuthenticationSession where available.
     On older iOS versions uses SFAuthenticationSession
     Doesn't allow any other webview type, so if either of these are not present, fails the request*/
    MSALWebviewTypeAuthenticationSession,
    
#if TARGET_OS_IPHONE
    
    /** Use SFSafariViewController for all versions. */
    MSALWebviewTypeSafariViewController,
    
#endif
    /** Use WKWebView */
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в [Потоки проверки подлинности и сценарии приложений](authentication-flows-app-scenarios.md)
