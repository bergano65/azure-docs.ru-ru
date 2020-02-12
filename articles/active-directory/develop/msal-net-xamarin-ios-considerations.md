---
title: Замечания по Xamarin iOS (MSAL.NET) | Службы
titleSuffix: Microsoft identity platform
description: Сведения о вопросах использования Xamarin iOS с библиотекой проверки подлинности Майкрософт для .NET (MSAL.NET).
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
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132469"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Рекомендации по использованию Xamarin iOS с MSAL.NET
При использовании библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET) в Xamarin iOS необходимо выполнить следующие действия: 

- Переопределите и реализуйте функцию `OpenUrl` в `AppDelegate`.
- Включите группы цепочки ключей.
- Включите совместное использование кэша маркеров.
- Включите доступ к цепочке ключей.
- Узнайте об известных проблемах с iOS 12 и проверкой подлинности.

## <a name="implement-openurl"></a>Реализация OpenUrl

Переопределите метод `OpenUrl` производного класса `FormsApplicationDelegate` и вызовите `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`. Ниже приведен пример:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Также выполните следующие задачи. 
* Определите схему URL-адресов.
* Требовать разрешения для приложения на вызов другого приложения.
* Иметь определенную форму для URL-адреса перенаправления.
* Зарегистрируйте URL-адрес перенаправления в [портал Azure](https://portal.azure.com).

### <a name="enable-keychain-access"></a>Включение доступа к цепочке ключей

Чтобы включить доступ к цепочке ключей, убедитесь, что у приложения есть группа доступа к цепочке ключей. Группу доступа к цепочке ключей можно задать при создании приложения с помощью `WithIosKeychainSecurityGroup()` API.

Чтобы воспользоваться преимуществами кэша и единого входа (SSO), задайте для группы доступа к цепочке ключей одно и то же значение во всех приложениях.

В этом примере программы установки используется MSAL 4. x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Также включите доступ к цепочке ключей в файле `Entitlements.plist`. Используйте следующую группу доступа или собственную группу доступа.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

При использовании API `WithIosKeychainSecurityGroup()` MSAL автоматически добавляет группу безопасности в конец *идентификатора группы* приложения (`AppIdentifierPrefix`). MSAL добавляет группу безопасности, так как при создании приложения в Xcode оно будет выполнять те же действия. Вот почему права в файле `Entitlements.plist` должны включать `$(AppIdentifierPrefix)` перед группой доступа к цепочке ключей.

Дополнительные сведения см. в документации по назначениям [iOS](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Включение общего доступа к кэшу маркеров в приложениях iOS

Начиная с MSAL 2. x можно указать группу доступа к цепочке ключей, чтобы сохранить кэш маркеров в нескольких приложениях. Этот параметр позволяет использовать кэш маркеров в нескольких приложениях с одной группой доступа к цепочке ключей. Вы можете поделиться наличными маркерами между [ADAL.NET](https://aka.ms/adal-net) приложениями, MSAL.NET приложениями Xamarin. iOS и собственными приложениями iOS, разработанными в [ADAL. ObjC](https://github.com/AzureAD/azure-activedirectory-library-for-objc) или [MSAL. ObjC](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

При совместном использовании кэша маркеров вы разрешаете единый вход (SSO) для всех приложений, использующих одну группу доступа к цепочке ключей.

Чтобы включить этот общий доступ к кэшу, используйте метод `WithIosKeychainSecurityGroup()`, чтобы задать для группы доступа к цепочке ключей одно и то же значение во всех приложениях, использующих общий кэш. В первом примере кода в этой статье показано, как использовать метод.

Ранее в этой статье вы узнали, что MSAL добавляет `$(AppIdentifierPrefix)` при каждом использовании `WithIosKeychainSecurityGroup()` API. MSAL добавляет этот элемент, так как идентификатор группы `AppIdentifierPrefix` гарантирует, что только приложения, созданные одним издателем, могут совместно использовать доступ к цепочке ключей.

> [!NOTE]
> Свойство `KeychainSecurityGroup` является устаревшим.
> 
> Начиная с MSAL 2. x разработчики вынуждены включать префикс `TeamId` при использовании свойства `KeychainSecurityGroup`. Но начиная с версии MSAL 2.7. x при использовании нового свойства `iOSKeychainSecurityGroup`, MSAL разрешает префикс `TeamId` во время выполнения. При использовании этого свойства не включайте префикс `TeamId` в значение. Префикс не требуется.
>
> Так как свойство `KeychainSecurityGroup` устарело, используйте свойство `iOSKeychainSecurityGroup`.

### <a name="use-microsoft-authenticator"></a>Использование Microsoft Authenticator

Приложение может использовать Microsoft Authenticator в качестве брокера для включения:

- **Единый**вход. при включении единого входа пользователям не нужно входить в каждое приложение.
- **Идентификация устройства**: используйте идентификацию устройства для проверки подлинности при доступе к сертификату устройства. Этот сертификат создается на устройстве при его присоединении к рабочей области. Приложение будет готово, если администраторы клиента включают условный доступ, связанный с устройствами.
- **Проверка идентификации приложения**. когда приложение вызывает брокер, ему передается URL-адрес перенаправления. Брокер проверяет URL-адрес перенаправления.

Дополнительные сведения о том, как включить брокер, см. [в статье использование Microsoft Authenticator или Microsoft Intune корпоративный портал в приложениях Xamarin iOS и Android](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Известные проблемы с iOS 12 и аутентификацией
Корпорация Майкрософт выпустила [Советы по безопасности](https://github.com/aspnet/AspNetCore/issues/4647) о несовместимости между iOS 12 и некоторыми типами проверки подлинности. Несовместимость нарушит вход в социальные, WSFed и OIDC. Советы по безопасности помогают разработчикам понять, как удалить ASP.NET ограничения безопасности из своих приложений, чтобы обеспечить их совместимость с iOS 12.  

При разработке MSAL.NET приложений в Xamarin iOS вы можете столкнуться с бесконечным циклом при попытке войти на веб-сайты из iOS 12. Такое поведение аналогично этой [ошибке ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Вы также можете увидеть перерыв в ASP.NET Core проверки подлинности OIDC в iOS 12 Safari. Дополнительные сведения см. в этой [WebKit статье](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о свойствах для Xamarin iOS см. в разделе [рекомендации для iOS](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) в следующем примере файла readme.md:

Образец | Платформа | Description
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, универсальная платформа Windows (UWP) | Простое приложение Xamarin Forms, которое показывает, как использовать MSAL для проверки подлинности личных учетных записей Майкрософт и Azure AD через конечную точку Azure AD 2,0. В приложении также показано, как использовать полученный маркер для доступа к Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->