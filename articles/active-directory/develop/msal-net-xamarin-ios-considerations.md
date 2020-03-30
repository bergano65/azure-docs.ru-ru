---
title: Соображения Xamarin iOS (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Узнайте о соображениях использования Xamarin iOS в библиотеке подлинности Microsoft для .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76e614b605cd07cd5dc454824dd204447f806907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262715"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Рассмотрение вопроса об использовании Xamarin iOS с MSAL.NET
При использовании библиотеки аутентификации Майкрософт для .NET (MSAL.NET) на Xamarin iOS следует: 

- Переопределить и `OpenUrl` реализовать `AppDelegate`функцию в .
- Включить группы брелоков.
- Включить совместное использование кэша токенов.
- Включить доступ к брелоку.
- Понимание известных проблем с iOS 12 и аутентификации.

## <a name="implement-openurl"></a>Реализация OpenUrl

Переопределить `OpenUrl` метод производного `FormsApplicationDelegate` `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`класса и вызвать . Ниже приведен пример:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Также выполняйте следующие задачи: 
* Определите схему URL- и
* Требуются разрешения для вызова приложения в другое приложение.
* Иметь конкретную форму для URL-адреса перенаправления.
* Зарегистрируйте URL-адрес перенаправления на [портале Azure](https://portal.azure.com).

### <a name="enable-keychain-access"></a>Включить доступ к брелоку

Чтобы включить доступ к брелоку, убедитесь, что ваше приложение имеет группу доступа к брелоку. При создании приложения можно настроить группу доступа к `WithIosKeychainSecurityGroup()` брелоку с помощью API.

Чтобы воспользоваться кэшом и единым ввозам (SSO), установите группу доступа к брелоку одинаковое значение во всех приложениях.

В этом примере установки используется MSAL 4.x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Также включите доступ `Entitlements.plist` к брелоку в файле. Используйте либо следующую группу доступа, либо собственную группу доступа.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

При использовании `WithIosKeychainSecurityGroup()` API MSAL автоматически прикладывает группу безопасности к концу *идентификатора команды* приложения ().`AppIdentifierPrefix` MSAL добавляет вашу группу безопасности, потому что при создании приложения в Xcode, он будет делать то же самое. Вот почему права в `Entitlements.plist` файле должны `$(AppIdentifierPrefix)` быть включены перед группой доступа к брелоку.

Для получения дополнительной информации ознакомьтесь с документацией о [правах iOS.](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps) 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Включить кэш маркеров в приложениях iOS

Начиная с MSAL 2.x, можно указать группу доступа к брелоку для сохранения кэша маркеров в нескольких приложениях. Эта настройка позволяет совместно использовать кэш маркеров между несколькими приложениями, которые имеют одну и ту же группу доступа к брелоку. Вы можете поделиться токенналичными деньгами между [ADAL.NET](https://aka.ms/adal-net) приложениями, MSAL.NET приложениями Xamarin.iOS и нативными приложениями iOS, которые были разработаны в [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) или [MSAL.objc.](https://github.com/AzureAD/microsoft-authentication-library-for-objc)

Совместное использование кэша маркеров позволяет развести (SSO) между всеми приложениями, которые используют одну и ту же группу доступа к брелоку.

Чтобы включить этот общий `WithIosKeychainSecurityGroup()` доступ к эшели, используйте метод для установки группы доступа к брелоку одинакового значения во всех приложениях, которые имеют один и тот же кэш. Первый пример кода в этой статье показывает, как использовать метод.

Ранее в этой статье вы узнали, что MSAL добавляет `$(AppIdentifierPrefix)` всякий раз, когда вы используете `WithIosKeychainSecurityGroup()` API. MSAL добавляет этот элемент, `AppIdentifierPrefix` поскольку идентификатор команды гарантирует, что только приложения, сделанные одним и тем же издателем, могут совместно с доступом к брелоку.

> [!NOTE]
> Имущество `KeychainSecurityGroup` обезвлечено.
> 
> Начиная с MSAL 2.x, разработчики `TeamId` были вынуждены `KeychainSecurityGroup` включить префикс, когда они использовали свойство. Но, начиная с MSAL 2.7.x, `iOSKeychainSecurityGroup` когда вы используете `TeamId` новое свойство, MSAL разрешает префикс во время выполнения. При использовании этого свойства не `TeamId` включайте приставку в значение. Приставка не требуется.
>
> Поскольку `KeychainSecurityGroup` свойство устарело, используйте свойство. `iOSKeychainSecurityGroup`

### <a name="use-microsoft-authenticator"></a>Использование Microsoft Authenticator

Ваше приложение может использовать Microsoft Authenticator в качестве брокера для включения:

- **SSO**: При включании SSO пользователям не нужно входить в каждое приложение.
- **Идентификация устройства**: Используйте идентификацию устройства для проверки подлинности при доступе к сертификату устройства. Этот сертификат создается на устройстве при присоединении к рабочему месту. Ваше приложение будет готово, если администраторы-арендаторы смогут обеспечить условный доступ, связанный с устройствами.
- **Проверка идентификации приложения:** Когда приложение звонит брокеру, оно передает свой url-адрес перенаправления. Брокер проверяет URL-адрес перенаправления.

Подробную информацию о том, как включить брокера, можно получить [в приложении Xamarin iOS и Android:](msal-net-use-brokers-with-xamarin-apps.md)

## <a name="known-issues-with-ios-12-and-authentication"></a>Известные проблемы с iOS 12 и аутентификацией
Корпорация Майкрософт выпустила рекомендации по [безопасности](https://github.com/aspnet/AspNetCore/issues/4647) о несовместимости между iOS 12 и некоторыми типами аутентификации. Несовместимость нарушает социальные, WSFed, и OIDC входе. Рекомендации по безопасности помогают разработчикам понять, как снять ограничения ASP.NET безопасности из своих приложений, чтобы сделать их совместимыми с iOS 12.  

При разработке MSAL.NET приложений на Xamarin iOS, вы можете увидеть бесконечный цикл, когда вы пытаетесь войти на веб-сайты с iOS 12. Такое поведение похоже на эту [проблему ADAL.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329) 

Вы также можете увидеть перерыв в ASP.NET Core OIDC аутентификации с iOS 12 Safari. Для получения дополнительной информации смотрите этот [выпуск WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="next-steps"></a>Дальнейшие действия

Для получения информации о свойствах xamarin iOS README.md см. [iOS-specific considerations](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations)

Пример | Платформа | Описание
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Универсальная платформа Windows (UWP) | Простое приложение Xamarin Forms, которое показывает, как использовать MSAL для проверки подлинности личных учетных записей Майкрософт и Azure AD через конечную точку Azure AD 2.0. Приложение также показывает, как использовать полученный маркер для доступа к Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->