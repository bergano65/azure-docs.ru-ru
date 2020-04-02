---
title: Общий режим устройства для устройств iOS
titleSuffix: Microsoft identity platform | Azure
description: Узнайте, как включить общий режим устройства, чтобы позволить работникам Firstline совместно с устройством iOS
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7cecbc48eb362c2c0f1741352e6f7f5f6ad40c9e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550257"
---
# <a name="shared-device-mode-for-ios-devices"></a>Общий режим устройства для устройств iOS

> [!NOTE]
> Эта функция предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Работники первой линии, такие как розничные партнеры, члены экипажа и работники полевых служб, часто используют совместное мобильное устройство для выполнения своей работы. Эти общие устройства могут представлять угрозу безопасности, если пользователи намеренно или нет обмениваются паролями или PIN-кодами для доступа к данным клиентов и бизнеса на совместном устройстве.

Режим общего устройства позволяет настроить iOS 13 или более устройство, чтобы быть более легко и безопасно совместно совместно сотрудниками. Сотрудники могут быстро войти в систему и получить доступ к информации о клиентах. Когда они закончат свою смену или задачу, они могут выйти из устройства, и он сразу же готов к использованию следующим сотрудником.

Режим совместного устройства также обеспечивает управление устройством, поддерживаемым удостоверением личности, корпорацией Майкрософт.

Эта функция использует [приложение Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) для управления пользователями на устройстве и распространения [плагина Microsoft Enterprise SSO для устройств Apple.](apple-sso-plugin.md)

## <a name="create-a-shared-device-mode-app"></a>Создание приложения общего режима устройства

Для создания приложения общего режима устройства разработчики и поставщики облачных устройств работают вместе:

1. **Разработчики приложений** пишут приложение с одной учетной записью (приложения с несколькими учетными данными не поддерживаются в режиме общего устройства) и пишут код для обработки таких вещей, как общий выход на устройство.

1. **Администраторы устройств** готовят устройство для совместного использования с помощью поставщика управления мобильными устройствами (MDM), такого как Microsoft Intune, для управления устройствами в их организации. MDM толкает приложение Microsoft Authenticator на устройства и включает "Общий режим" для каждого устройства через обновление профиля на устройстве. Эта настройка общего режима является то, что изменяет поведение поддерживаемых приложений на устройстве. Эта конфигурация от поставщика MDM устанавливает общий режим устройства для устройства и позволяет [microsoft Enterprise SSO плагин для устройств Apple,](apple-sso-plugin.md) который необходим для общего режима устройства.

1. Требуется**только во время публичного предварительного просмотра** Пользователь с ролью [администратора облачных устройств](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator) должен затем запустить [приложение Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) и присоединиться к своему устройству к организации.

    Настройка членского состава организационных ролей на портале Azure: **функции активных каталогов** > Azure**и администраторы облачных** > **устройств**

Следующие разделы помогут вам обновить приложение для поддержки режима общего устройства.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Используйте Intune для включения режима совместного устройства & расширение SSO

> [!NOTE]
> Следующий шаг требуется только во время публичного предварительного просмотра.

Устройство должно быть настроено для поддержки режима общего устройства. Он должен иметь iOS 13 "установлен и быть MDM-зарегистрированы. Конфигурация MDM также должна включать [плагин Microsoft Enterprise SSO для устройств Apple.](apple-sso-plugin.md) Чтобы узнать больше о расширениях SSO, смотрите [видео Apple](https://developer.apple.com/videos/play/tech-talks/301/).

1. На портале конфигурации Intune сообщите устройству, чтобы включить [плагин Microsoft Enterprise SSO для устройств Apple](apple-sso-plugin.md) со следующей конфигурацией:

    - **Тип**: Перенаправление
    - **Идентификатор расширения**: com.microsoft.azureauthenticator.ssoextension
    - **Идентификатор команды**: SGGM6D27TK
    - **URL-адреса**:https://login.microsoftonline.com
    - Дополнительные данные для настройки:
      - Ключ: общийDeviceMode
      - Тип: логический
      - Значение: Правда

    Для получения дополнительной информации о настройке [Intune configuration documentation](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)с Intune, см.

1. Затем найди MDM, чтобы нажать приложение Microsoft Authenticator на устройство через профиль MDM.

    Установите следующие параметры конфигурации, чтобы включить режим общего устройства:

    - Конфигурация 1.
      - Ключ: общийDeviceMode
      - Тип: логический
      - Значение: Правда

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Измените приложение iOS для поддержки режима общего устройства

Ваши пользователи зависят от вас, чтобы убедиться, что их данные не просочились другому пользователю. Следующие разделы предоставляют полезные сигналы, указывающие на ваше приложение, что произошло изменение и должно быть обработано.

Вы несете ответственность за проверку состояния пользователя на устройстве при каждом использовании приложения, а затем за очистку данных предыдущего пользователя. Это включает в себя, если он перезагружается из фона в многозадачности.

При изменении пользователя следует обеспечить удаление данных предыдущего пользователя и удаление любых кэшированных данных, отображаемых в приложении. Мы настоятельно рекомендуем вам и вашей компании провести процесс проверки безопасности после обновления приложения для поддержки режима общего устройства.

### <a name="detect-shared-device-mode"></a>Обнаружение общего режима устройства

Обнаружение режима общего устройства важно для вашего приложения. Многие приложения потребуют изменения в их пользовательском опыте (UX), когда приложение используется на общем устройстве. Например, в приложении может быть функция "Sign-Up", которая не подходит для первого работника, поскольку у него, вероятно, уже есть учетная запись. Вы также можете добавить дополнительную безопасность в обработку данных приложения, если оно в режиме общего устройства.

Используйте `getDeviceInformationWithParameters:completionBlock:` API, `MSALPublicClientApplication` чтобы определить, работает ли приложение на устройстве в режиме общего устройства.

Следующие фрагменты кода показывают примеры использования `getDeviceInformationWithParameters:completionBlock:` API.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Получить вписав пользователя и определить, если пользователь изменил сяопову на устройстве

Другой важной частью поддержки режима общего устройства является определение состояния пользователя на устройстве и очистка данных приложения, если пользователь изменился или если нет пользователя вообще на устройстве. Вы несете ответственность за то, чтобы данные не просочились другому пользователю.

Можно использовать `getCurrentAccountWithParameters:completionBlock:` API для запроса учетной записи, в настоящее время подписанной на устройстве.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>Глобальный вход пользователя

Когда устройство настроено как общее устройство, приложение `acquireTokenWithParameters:completionBlock:` может вызвать API для регистрации в учетной записи. Учетная запись будет доступна во всем мире для всех подходящих приложений на устройстве после того, как первые признаки приложения в учетной записи.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Глобальный выход пользователя

Следующий код удаляет учетную запись в подписанной записи и очищает кэшированные токены не только из приложения, но и с устройства, накотороговавшего в режиме общего устройства. Однако *данные* из приложения не очищаетесь. Необходимо очистить данные из приложения, а также очистить все кэшированные данные, которые приложение может отображать пользователю.

#### <a name="clear-browser-state"></a>Четкое состояние браузера

> [!NOTE]
> Следующий шаг требуется только во время публичного предварительного просмотра.

В этой публичной версии предварительного просмотра [плагин Microsoft Enterprise SSO для устройств Apple](apple-sso-plugin.md) очищает состояние только для приложений. Это не ясно состояние на браузере Safari. Мы рекомендуем вам вручную очистить сеанс браузера, чтобы убедиться, что никаких следов состояния пользователя не осталось позади. Вы можете использовать `signoutFromBrowser` дополнительное свойство, указанное ниже, чтобы очистить все файлы cookie. Это приведет к кратковременному запуску браузера на устройстве.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы увидеть общий режим устройства в действии, следующий образец кода на GitHub включает в себя пример запуска приложения Firstline Worker на устройстве iOS в режиме общего устройства:

[MSAL IOS Swift Microsoft Граф API Пример](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
