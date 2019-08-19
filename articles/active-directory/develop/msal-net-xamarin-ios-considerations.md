---
title: Замечания по Xamarin iOS (Библиотека проверки подлинности Microsoft для .NET) | Службы
description: Ознакомьтесь с конкретными соображениями при использовании Xamarin iOS с библиотекой проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 906f2fc8cdac31922e6e93526f65577fe76c4b9c
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532348"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Вопросы, связанные с Xamarin iOS, с MSAL.NET
В Xamarin iOS существует несколько аспектов, которые необходимо учитывать при использовании MSAL.NET.

- [Известные проблемы с iOS 12 и аутентификацией](#known-issues-with-ios-12-and-authentication)
- [Переопределите и реализуйте `OpenUrl` функцию в`AppDelegate`](#implement-openurl)
- [Включение групп ключей цепочки](#enable-keychain-access)
- [Включить общий доступ к кэшу маркеров](#enable-token-cache-sharing-across-ios-applications)
- [Включение доступа к цепочке ключей](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Известные проблемы с iOS 12 и аутентификацией
Корпорация Майкрософт выпустила [Советы по безопасности](https://github.com/aspnet/AspNetCore/issues/4647) , чтобы предоставить сведения о несовместимости между iOS12 и некоторыми типами проверки подлинности. Несовместимость нарушает имена входа в социальных сетях, WSFed и OIDC. В этой статье также приводятся рекомендации по удалению текущих ограничений безопасности, добавленных ASP.NET к приложениям для обеспечения совместимости с iOS12.  

При разработке MSAL.NET приложений в Xamarin iOS вы можете столкнуться с бесконечным циклом при попытке войти на веб-сайты из iOS 12 (подобно этой [ошибке ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Вы также можете увидеть перерыв в ASP.NET Core проверки подлинности OIDC в iOS 12 Safari, как описано в этой [статье WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>Реализация OpenUrl

Сначала необходимо переопределить `OpenUrl` метод `FormsApplicationDelegate` производного класса и вызвать `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`метод.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Кроме того, необходимо определить схему URL-адресов, запросить у приложения разрешения на вызов другого приложения, указать определенную форму для URL-адреса перенаправления и зарегистрировать этот URL-адрес перенаправления в [портал Azure](https://portal.azure.com)

### <a name="enable-keychain-access"></a>Включение доступа к цепочке ключей

Чтобы включить доступ к цепочке ключей, приложение должно иметь группу доступа к цепочке ключей.
Вы можете задать группу доступа к цепочке ключей с `WithIosKeychainSecurityGroup()` помощью API при создании приложения, как показано ниже:

Чтобы включить единый вход, необходимо присвоить `PublicClientApplication.iOSKeychainSecurityGroup` свойству одно и то же значение во всех приложениях.

Примером этого является использование MSAL v3. x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Права. plist должны быть обновлены, чтобы выглядеть так, как в следующем фрагменте XML:

Это изменение является *дополнением* к включению доступа к цепочке ключей в `Entitlements.plist` файле с помощью любой из следующих групп доступа:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.msalrocks</string>
  </array>
</dict>
</plist>
```

Пример использования MSAL v4. x:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

При использовании `WithIosKeychainSecurityGroup()` API MSAL автоматически добавит группу безопасности в конец "идентификатора группы" (AppIdentifierPrefix) приложения, так как при создании приложения с помощью Xcode он сделает то же самое. [Дополнительные сведения см. в документации по](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)назначениям iOS. Поэтому необходимо обновить права для включения $ (AppIdentifierPrefix) перед группой доступа к цепочке ключей в списке прав. plist.

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Включение общего доступа к кэшу маркеров в приложениях iOS

Из MSAL 2. x можно указать группу доступа к цепочке ключей, которая будет использоваться для сохранения кэша маркеров в нескольких приложениях. Этот параметр позволяет использовать кэш маркеров между несколькими приложениями, имеющими одну группу доступа к цепочке ключей, включая те, которые разработаны с помощью [ADAL.NET](https://aka.ms/adal-net), MSAL.NET приложения Xamarin. iOS и собственные приложения iOS, разработанные с помощью [ ADAL. ObjC](https://github.com/AzureAD/azure-activedirectory-library-for-objc) или [MSAL. ObjC](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

Совместное использование кэша маркеров обеспечивает единый вход для всех приложений, использующих одну группу доступа к цепочке ключей.

Чтобы включить этот общий доступ к кэшу, необходимо установить параметр с помощью метода "Висиоскэйчаинсекуритиграуп ()", чтобы задать для группы доступа к цепочке ключей одинаковое значение во всех приложениях, совместно использующих тот же кэш, как показано в примере выше.

Ранее было сказано, что MSAL добавила $ (AppIdentifierPrefix) при каждом использовании `WithIosKeychainSecurityGroup()` API. Это связано с тем, что AppIdentifierPrefix или «идентификатор команды» используются, чтобы обеспечить совместное использование доступа к цепочке ключей только для приложений, созданных одним издателем.

#### <a name="note-keychainsecuritygroup-property-deprecated"></a>Примечание. Нерекомендуемое свойство Кэйчаинсекуритиграуп

Ранее в MSAL 2. x разработчики вынуждены включать префикс теамид при использовании `KeychainSecurityGroup` свойства.

В MSAL 2.7. x при использовании нового `iOSKeychainSecurityGroup` свойства MSAL будет разрешать префикс теамид во время выполнения. При использовании этого свойства значение не должно содержать префикс Теамид.

Используйте новое `iOSKeychainSecurityGroup` свойство, которое не требует от разработчиков предоставления теамид, так как предыдущее `KeychainSecurityGroup` свойство устарело.

### <a name="sample-illustrating-xamarin-ios-specific-properties"></a>Пример, иллюстрирующий конкретные свойства Xamarin iOS

Дополнительные сведения см. в абзаце с описанием особенностей [iOS](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) в следующем примере файла readme.md:

Пример | Платформа | Описание
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP; | Простое приложение Xamarin Forms, демонстрирующие использование MSAL для проверки подлинности MSA и Azure AD через конечную точку Azure AD версии 2.0, а также доступа к Microsoft Graph с полученным маркером.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
