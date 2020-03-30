---
title: Настройка цепочки ключей
titleSuffix: Microsoft identity platform
description: Узнайте, как настроить брелок таким образом, чтобы приложение мог кэшировать токены в брелоке.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d94bf7ffe955c9ec9ee2a2e7f7c4dbaaa28df270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085865"
---
# <a name="configure-keychain"></a>Настройка цепочки ключей

Когда [Библиотека аутентификации Майкрософт для iOS и macOS](msal-overview.md) (MSAL) подписывает в пользователе или обновляет токен, она пытается кэшировать токены в брелоке. Кэширование токенов в брелоке позволяет MSAL предоставлять бесшумный одиночный ввод (SSO) между несколькими приложениями, которые распространяются одним и тем же разработчиком Apple. SSO достигается с помощью функциональности групп доступа к брелоку. Для получения дополнительной информации [Keychain Items documentation](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)см.

В этой статье рассказывается о том, как настроить права приложений таким образом, чтобы MSAL мог писать кэшированные токены на брелок iOS и macOS.

## <a name="default-keychain-access-group"></a>Группа доступа к брелоку по умолчанию

### <a name="ios"></a>iOS

MSAL на iOS `com.microsoft.adalcache` использует группу доступа по умолчанию. Это общая группа доступа, используемая как MSAL, так и SD-библиотекой aD AD (ADAL) и обеспечивает лучший опыт в один очном входе (SSO) между несколькими приложениями одного и того же издателя.

На iOS `com.microsoft.adalcache` добавьте группу брелоков в право вашего приложения в XCode в **настройках** > проекта**Capabilities** > **Keychain**

### <a name="macos"></a>macOS

MSAL на macOS использует `com.microsoft.identity.universalstorage` группу доступа по умолчанию.

Из-за ограничений на брелок macOS MSAL `access group` напрямую переводится на атрибут группы доступа к брелоку (см. [kSecAttrAccessGroup)](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)на macOS 10.14 и ранее. Тем не менее, он ведет себя аналогично с точки зрения SSO, гарантируя, что несколько приложений, распространяемых одним и тем же разработчиком Apple, могут иметь молчаливый SSO.

На macOS 10.15 года (macOS Catalina) MSAL использует атрибут группы доступа к брелоку для достижения бесшумной SSO, подобно iOS.

## <a name="custom-keychain-access-group"></a>Пользовательская группа доступа к брелоку

Если вы хотите использовать другую группу доступа к брелоку, `MSALPublicClientApplicationConfig` вы `MSALPublicClientApplication`можете пройти пользовательскую группу при создании перед созданием, как это:

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>Отключите обмен брелоком

Если вы не хотите делиться состоянием SSO между несколькими приложениями или использовать любую группу доступа к брелоку, отключите обмен брелока, передав идентификатор пакета приложения в качестве брелокаГруппы:

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Ошибка обработки -34018 (не установить элемент в брелок)

Ошибка -34018 обычно означает, что брелок не был настроен правильно. Убедитесь, что группа доступа к брелоку, настроенная в MSAL, соответствует той, которая настроена в правах.

## <a name="ensure-your-application-is-properly-signed"></a>Убедитесь, что приложение подписано правильно

На macOS приложения могут выполняться без подписи разработчика. В то время как большая часть функциональности MSAL будет продолжать работать, SSO через доступ к брелоку требует подписи приложения. Если у вас возникло несколько запросов на брелок, убедитесь, что подпись приложения действительна.

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о группах доступа к брелоку читайте в материале журнала "Огонек" ["Доступ к ключевым цепочкам среди](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) раздела приложений".
