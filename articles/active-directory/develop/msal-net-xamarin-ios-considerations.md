---
title: Рекомендации по iOS Xamarin (библиотека проверки подлинности Майкрософт для .NET) | Azure
description: Дополнительные сведения о специальные рекомендации при использовании Xamarin iOS с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7621b18f32a90b81dc075c534367981cf44f16e0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076335"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Рекомендации по конкретной iOS Xamarin с помощью MSAL.NET
В Xamarin iOS учитывать несколько моментов, которые следует принять во внимание при использовании MSAL.NET

- [Известные проблемы с iOS 12 и проверки подлинности](#known-issues-with-ios-12-and-authentication)
- [Переопределите и реализуйте `OpenUrl` работать в `AppDelegate`](#implement-openurl)
- [Включение групп цепочки ключей](#enable-keychain-groups)
- [Включить общий доступ к кэш маркеров](#enable-token-cache-sharing-across-ios-applications)
- [Включить доступ к цепочке ключей](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Известные проблемы с iOS 12 и проверки подлинности
Корпорация Майкрософт выпустила [совет по безопасности](https://github.com/aspnet/AspNetCore/issues/4647) для предоставления сведений о несовместимости iOS12 и некоторые виды проверки подлинности. Несовместимость социального разрывы, WSFed и OIDC учетных записей. Рекомендации также предоставляет инструкции по разработчики могут использовать для удаления текущими ограничениями безопасности, добавленные ASP.NET к приложениям для обеспечения совместимости с iOS12.  

При разработке приложений MSAL.NET на Xamarin iOS, может появиться бесконечный цикл при попытке войти в веб-сайтов из iOS 12 (примерно [ADAL проблема](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Также возможны прерывания в работе проверки подлинности ASP.NET Core OIDC с iOS 12 Safari, как описано в этой [WebKit проблема](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>Реализуйте OpenUrl

Сначала необходимо переопределить `OpenUrl` метод `FormsApplicationDelegate` производного класса, а также вызов `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Необходимо также определить схему URL-адресов, требуются разрешения для вызова другого приложения, имеют определенные формы для URL-адрес перенаправления и зарегистрировать этот URL-адрес перенаправления в приложении [портала Azure](https://portal.azure.com).

## <a name="enable-keychain-groups"></a>Включение групп цепочки ключей

Чтобы сделать маркер кэшировать рабочие и иметь `AcquireTokenSilentAsync` метод работы, необходимо выполнить несколько шагов:
1. Включить доступ к цепочке ключей в вашей *`* Entitlements.plist* файла и укажите **групп цепочек ключей** в идентификатор пакета.
2. Выберите *`*Entitlements.plist*`* файл **настраиваемые назначения** поле в окне параметров iOS проекта **представление подписи пакета**.
3. При подписи сертификата, убедитесь, что XCode использует тот же идентификатор Apple.

## <a name="enable-token-cache-sharing-across-ios-applications"></a>Включить кэш маркеров, совместное использование приложений iOS

Начиная с версии MSAL 2.x, можно указать группу безопасности цепочки ключей для сохранения кэша маркера в нескольких приложениях. Это дает возможность совместного использования кэша маркера между несколькими приложениями, иметь ту же группу безопасности цепочки ключей, в том числе разработанных с помощью [ADAL.NET](https://aka.ms/adal-net), MSAL.NET Xamarin.iOS приложений и приложений на машинном коде iOS, разработанных с помощью [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) или [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

Совместное использование кэш маркеров, позволяет единого входа (SSO), между всеми приложениями, использующими ту же группу безопасности в цепочке ключей.

Чтобы включить единый вход, необходимо задать `PublicClientApplication.iOSKeychainSecurityGroup` в соответствии с тем же значением во всех приложений.

Примером этого с помощью MSAL v3.x бы:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Примером этого с помощью MSAL v2.7.x бы:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

> [!NOTE]
> `KeychainSecurityGroup` Оно устарело. Ранее, в MSAL 2.x, разработчикам приходилось включать префикс TeamId, при использовании `KeychainSecurityGroup` свойство. 
> 
> Теперь, начиная с версии MSAL 2.7.x, MSAL разрешит префикс TeamId во время выполнения при использовании `iOSKeychainSecurityGroup` свойство. При использовании этого свойства, значение не должно содержать префикс TeamId. 
> 
> Используйте новый `iOSKeychainSecurityGroup` свойство, которое не требует от разработчиков для обеспечения TeamId. `KeychainSecurityGroup` Свойство устарело. 

## <a name="enable-keychain-access"></a>Включить доступ к цепочке ключей

В MSAL 2.x и ADAL 4.x TeamId используется для доступа к цепочке ключей, позволяющий библиотеки проверки подлинности для предоставления единого входа (SSO) между приложений одного издателя. 

Что такое [TeamIdentifierPrefix](/xamarin/ios/deploy-test/provisioning/entitlements?tabs=vsmac) (TeamId)? Это уникальный идентификатор (компания или личное) в App Store. Идентификатор приложения является уникальным для приложения. Если у вас есть несколько приложений, TeamId для всех приложений будет таким же, но AppId будет отличаться. Группы доступа к цепочке ключей предшествует TeamId автоматически для каждой группы в системе. Это, как операционная система обеспечивает, что приложений одного издателя, можно получить доступ к общей цепочке ключей. 

При инициализации `PublicClientApplication`, получив `MsalClientException` с сообщением: `TeamId returned null from the iOS keychain...`, необходимо выполните следующие действия в приложение Xamarin iOS:

1. В Visual STUDIO на вкладке отладки перейти к nameOfMyApp.iOS свойства...
2. Перейдите к подписывание пакета iOS 
3. В разделе настраиваемые назначения, нажмите кнопку... и выберите файл Entitlements.plist из приложения
4. В CSPROJ-файла приложения iOS вам необходимо иметь этой строки теперь включены: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. **Перестроить** проекта.

Это *кроме* предоставить доступ к цепочке ключей в `Entitlements.plist` файле, с использованием либо ниже группы доступа или собственные:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
</plist>
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения приведены в [iOS специальные рекомендации](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) абзац файл readme.md в следующем примере:

Образец | платформа | ОПИСАНИЕ 
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP; | Простое приложение Xamarin Forms, с отображением использование MSAL для проверки подлинности MSA и Azure AD через конечную точку версии 2.0 AAD и получить доступ к Microsoft Graph с помощью полученного маркера. <br>![Топология](media/msal-net-xamarin-ios-considerations/topology.png)