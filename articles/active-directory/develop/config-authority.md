---
title: Накончать поставщиков идентификационных данных (MSAL iOS/macOS) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как использовать различные органы власти, такие как B2C, суверенные облака и гостевые пользователи, с MSAL для iOS и macOS.
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
ms.openlocfilehash: 4810de772e44be22ee5bd4a9fb6ef0ef756e62f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085208"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Как: Налаживать MSAL для iOS и macOS для использования различных поставщиков идентификационных данных

В этой статье будет показан способ настройки приложения библиотеки подлинности Майкрософт для iOS и macOS (MSAL) для различных органов власти, таких как Azure Active Directory (Azure AD), Business-to-Consumer (B2C), суверенные облака и гостевые пользователи.  На протяжении всей этой статьи, как правило, можно думать о органе в качестве поставщика идентификационных данных.

## <a name="default-authority-configuration"></a>Конфигурация полномочий по умолчанию

`MSALPublicClientApplication`настроен с URL-адресом `https://login.microsoftonline.com/common`авторитета по умолчанию, который подходит для большинства сценариев Azure Active Directory (AAD). Если вы не реализуете расширенные сценарии, такие как национальные облака, или работаете с B2C, вам не нужно будет его менять.

> [!NOTE]
> Современная аутентификация с помощью службы Active Directory Federation в качестве поставщика идентификационных данных (ADFS) не поддерживается (подробнее см. [ADFS для разработчиков).](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) ADFS поддерживается через федерацию.

## <a name="change-the-default-authority"></a>Изменение органа по умолчанию

В некоторых сценариях, таких как бизнес-потребитель (B2C), может потребоваться изменить полномочия по умолчанию.

### <a name="b2c"></a>B2C

Для работы с B2C [библиотека подлинности Майкрософт (MSAL)](reference-v2-libraries.md) требует другой конфигурации органов власти. MSAL признает один формат URL-адреса авторитета как B2C сам по себе. Признанный формат b2C `https://<host>/tfp/<tenant>/<policy>`власти, `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`например . Тем не менее, вы также можете использовать любые другие поддерживаемые URL-адреса b2C, объявляя полномочия полномочиями B2C явно.

Для поддержки произвольного формата `MSALB2CAuthority` URL для B2C можно настроить произвольный URL-

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Все органы B2C, которые не используют формат полномочий По умолчанию B2C, должны быть объявлены известными властями.

Добавьте все различные полномочия B2C в список известных властей, даже если власти различаются только в политике.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

Когда приложение запрашивает новую политику, URL-адрес органа должен быть изменен, поскольку URL-адрес органа отличается для каждой политики. 

Чтобы настроить приложение B2C, `@property MSALAuthority *authority` установить `MSALB2CAuthority` с `MSALPublicClientApplicationConfig` экземпляром в перед созданием, `MSALPublicClientApplication`как это:

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Суверенные облака

Если ваше приложение работает в суверенном облаке, `MSALPublicClientApplication`возможно, потребуется изменить URL-адрес органа в . Следующий пример устанавливает URL-адрес органа для работы с немецким облаком AAD:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Возможно, вам придется передавать различные области каждому суверенному облаку. Какие области для отправки зависит от ресурса, который вы используете. Например, можно `"https://graph.microsoft.com/user.read"` использовать в облаке по всему миру и `"https://graph.microsoft.de/user.read"` в немецком облаке.

### <a name="signing-a-user-into-a-specific-tenant"></a>Подписание пользователя в конкретного арендатора

Когда URL-адрес органа `"login.microsoftonline.com/common"`установлен на, пользователь будет подписан в их домашнем арендаторе. Тем не менее, некоторые приложения могут потребоваться подписать пользователя в другой арендатор, а некоторые приложения работают только с одним арендатором.

Чтобы подписать пользователя в конкретного `MSALPublicClientApplication` арендатора, назначай с конкретным органом. Пример:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Ниже показано, как подписать пользователя в конкретного арендатора:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Поддерживаемые органы власти

### <a name="msalauthority"></a>МСАЛОрган

Класс `MSALAuthority` является базовым абстрактным классом для классов полномочий MSAL. Не пытайтесь создать экземпляр `alloc` его `new`использования или . Вместо этого либо создайте один`MSALAADAuthority`из `MSALB2CAuthority`его подклассов `authorityWithURL:error:` напрямую (, ) или используйте заводской метод для создания подклассов с использованием URL-адреса.

Используйте `url` свойство, чтобы получить нормализованный URL-адрес органа. Дополнительные параметры и компоненты пути или фрагменты, не входят в состав органа власти, не будут в возвращенном нормализованном URL-адресе.

Ниже приведены `MSALAuthority` подклассы, которые вы можете мгновенно в зависимости от органа хотите использовать.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority`представляет орган AAD. Url-адрес органа должен быть в `<port>` следующем формате, где необязательно:`https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CОрган

`MSALB2CAuthority`представляет b2C власти. По умолчанию URL-адрес B2C должен быть `<port>` в следующем `https://<host>:<port>/tfp/<tenant>/<policy>`формате, где необязательно: . Тем не менее, MSAL также поддерживает другие произвольные форматы полномочий B2C.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в [Потоки проверки подлинности и сценарии приложений](authentication-flows-app-scenarios.md)
