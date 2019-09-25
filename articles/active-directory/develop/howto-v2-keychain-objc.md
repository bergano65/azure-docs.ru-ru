---
title: Настройка цепочки ключей | Платформа Microsoft Identity
description: Узнайте, как настроить цепочку ключей, чтобы приложение могла кэшировать маркеры в цепочке ключей.
services: active-directory
documentationcenter: ''
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e85fc5e6e907e32c0ad67af339c48cf84ef4764
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269042"
---
# <a name="configure-keychain"></a>Настройка цепочки ключей

Когда [Библиотека проверки подлинности Microsoft для iOS и macOS](msal-overview.md) (MSAL) входит в пользователь или обновляет маркер, он пытается кэшировать токены в цепочке ключей. При кэшировании токенов в цепочке ключей MSAL может предоставлять автоматический единый вход (SSO) между несколькими приложениями, распространяемыми одним и тем же разработчиком Apple. Единый вход осуществляется с помощью функций групп доступа к цепочке ключей (см. [документацию Apple](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)).

В этой статье описывается, как настроить права приложений, чтобы MSAL мог записывать кэшированные маркеры в ОС iOS и macOS цепочку ключей.

## <a name="default-keychain-access-group"></a>Группа доступа к цепочке ключей по умолчанию

### <a name="ios"></a>iOS

По умолчанию MSAL в `com.microsoft.adalcache` iOS использует группу доступа. Это группа общего доступа, используемая как в пакетах SDK MSAL, так и в библиотеке аутентификация Azure AD Library (ADAL), и обеспечивает оптимальное взаимодействие единого входа между несколькими приложениями из одного издателя.

`com.microsoft.adalcache` В iOS добавьте группу цепочки ключей в назначение вашего приложения в Xcode в > разделе **Параметры** > проекта**общий доступ к цепочке ключей**

### <a name="macos"></a>macOS

MSAL в macOS использует `com.microsoft.identity.universalstorage` группу доступа по умолчанию.

Из-за ограничений macOS цепочки ключей `access group` MSAL не переводится непосредственно в атрибут группы доступа к цепочке ключей (см. [ксекаттракцессграуп](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)) в macOS 10,14 и более ранних версиях. Однако он ведет себя аналогично с точки зрения единого входа, гарантируя, что несколько приложений, распространяемых одним и тем же разработчиком Apple, могут иметь автоматический единый вход.

В macOS 10,15 (macOS Catalina) MSAL использует атрибут группы доступа к цепочке ключей для автоматического входа в систему, аналогично iOS.

## <a name="custom-keychain-access-group"></a>Пользовательская группа доступа к цепочке ключей

Если вы хотите использовать другую группу доступа к цепочке ключей, можно передать пользовательскую группу при создании `MSALPublicClientApplicationConfig` перед созданием `MSALPublicClientApplication`, например:

Objective-C.

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



SWIFT

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



## <a name="disable-keychain-sharing"></a>Отключить общий доступ к цепочке ключей

Если вы не хотите совместно использовать единый режим единого входа для нескольких приложений или группу доступа к цепочке ключей, отключите общий доступ к цепочке ключей, передав идентификатор пакета приложений в качестве Кэйчаинграуп:

Objective-C.

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

SWIFT

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Handle-34018 Error (не удалось установить элемент в цепочку ключей)

Ошибка-34018 обычно означает, что цепочка ключей не настроена правильно. Убедитесь, что Группа доступа к цепочке ключей, настроенная в MSAL, соответствует настроенной в правах.

## <a name="ensure-your-application-is-properly-signed"></a>Убедитесь, что приложение подписано правильно

В macOS приложения могут выполняться без подписи разработчика. Хотя большая часть функций MSAL будет продолжать работать, единый вход через цепочку ключей требует, чтобы приложение было подписано. Если у вас есть несколько запросов на цепочку ключей, убедитесь, что подпись приложения действительна.

## <a name="next-steps"></a>Следующие шаги

Узнайте больше о группах доступа к цепочке ключей в [общем доступе Apple к элементам цепочки ключей из статьи коллекция приложений](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) .
