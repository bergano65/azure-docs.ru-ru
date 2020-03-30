---
title: SSO между приложениями ADAL & MSAL (iOS/macOS) - платформа идентификации Майкрософт Azure
description: ''
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 2fbb6e837ae898daf4bc78d5cccc75660463e8a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085417"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Как: SSO между приложениями ADAL и MSAL на macOS и iOS

Библиотека аутентификации Майкрософт (MSAL) для iOS может совместно с [ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) обмениваться между приложениями. Вы можете перенести свои приложения в MSAL в своем собственном темпе, гарантируя, что ваши пользователи будут по-прежнему пользоваться кросс-приложение SSO - даже с сочетанием ADAL и MSAL основе приложений.

Если вы ищете руководство по настройке SSO между приложениями, использующими MSAL SDK, [см.](single-sign-on-macos-ios.md#silent-sso-between-apps) В этой статье основное внимание уделяется SSO между ADAL и MSAL.

Специфика реализации SSO зависит от версии ADAL, которую вы используете.

## <a name="adal-27x"></a>ADAL 2.7.x

Этот раздел охватывает различия SSO между MSAL и ADAL 2.7.x

### <a name="cache-format"></a>Формат кэша

ADAL 2.7.x может прочитать формат кэша MSAL. Вам не нужно делать ничего особенного для кросс-приложения SSO с версией ADAL 2.7.x. Однако необходимо знать о различиях в идентификаторах учетных записей, которые поддерживают эти две библиотеки.

### <a name="account-identifier-differences"></a>Различия идентификатора учетной записи

MSAL и ADAL используют различные идентификаторы учетных записей. ADAL использует UPN в качестве основного идентификатора учетной записи. MSAL использует идентификатор неотображаемой учетной записи, основанный на `sub` идентификаторе объекта и идентификаторе клиента для учетных записей AAD, а также претензию для других типов учетных записей.

Когда объект `MSALAccount` получается в результате MSAL, он содержит `identifier` идентификатор учетной записи в свойстве. Приложение должно использовать этот идентификатор для последующих запросов для немых.

В дополнение `identifier` `MSALAccount` к объекту содержит отображаемый идентификатор под названием `username`. Это переводится `userId` как в ADAL. `username`не считается уникальным идентификатором и может изменяться в любое время, поэтому он должен использоваться только для обратных сценариев совместимости с ADAL. MSAL поддерживает запросы кэша, используя либо `username` или `identifier`, где рекомендуется запрашивать. `identifier`

В следующей таблице кратко излагаются различия в идентификаторах учетных записей между ADAL и MSAL:

| Идентификатор учетной записи                | MSAL                                                         | ADAL 2.7.x      | Старый ADAL (до ADAL 2.7.x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| отображаемый идентификатор            | `username`                                                   | `userId`        | `userId`                       |
| уникальный неотображаемый идентификатор | `identifier`                                                 | `homeAccountId` | Недоступно                            |
| Не известен идентификатор учетной записи               | Запрос всех учетных записей через `allAccounts:` API в`MSALPublicClientApplication` | Недоступно             | Недоступно                            |

Это интерфейс, обеспечивающий `MSALAccount` эти идентификаторы:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>SSO от MSAL до ADAL

Если у вас есть приложение MSAL и приложение ADAL, и пользователь первым и поведает в приложении `username` на `MSALAccount` основе MSAL, вы можете получить `userId`SSO в приложении ADAL, сохранив его от объекта и передав его в ваше приложение на основе ADAL как. После этого ADAL может бесшумно `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` находить информацию об учетной записи в API.

### <a name="sso-from-adal-to-msal"></a>SSO от ADAL до MSAL

Если у вас есть приложение MSAL и приложение ADAL, и пользователь первым и впервые попадает в приложение на базе ADAL, вы можете использовать идентификаторы пользователей ADAL для поиска учетных записей в MSAL. Это также относится к миграции из ADAL в MSAL.

#### <a name="adals-homeaccountid"></a>Домашний счет АДАЛ

ADAL 2.7.x `homeAccountId` возвращает `ADUserInformation` объект в результате через это свойство:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId`в ADAL эквивалентно `identifier` в MSAL. Этот идентификатор можно сохранить в MSAL для `accountForIdentifier:error:` поиска учетных записей с помощью API.

#### <a name="adals-userid"></a>АДАЛ`userId`

Если `homeAccountId` вы недоступны или у вас есть только отображаемый идентификатор, вы можете использовать ADAL `userId` для поиска учетной записи в MSAL.

В MSAL сначала найди `username` `identifier`учетную запись или . Всегда `identifier` используйте для запроса, если он `username` у вас есть, и использовать только в качестве резервного. Если учетная запись найдена, `acquireTokenSilent` используйте учетную запись в вызовах.

Objective-C.

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



MSAL поддерживает AUP поиска учетной записи:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2.x-2.6.6

Этот раздел охватывает различия SSO между MSAL и ADAL 2.x-2.6.6.

Старые версии ADAL не поддерживают формат кэша MSAL. Однако, чтобы обеспечить плавную миграцию из ADAL в MSAL, MSAL может прочитать старый формат кэша ADAL без повторного запроса на учетные данные пользователей.

Поскольку `homeAccountId` в старых версиях ADAL нет доступных версий `username`ADAL, необходимо искать учетные записи с помощью:

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Пример:

Objective-C.


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



Кроме того, вы можете прочитать все счета, которые также будут читать информацию об учетной записи от ADAL:

Objective-C.

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в [Потоки проверки подлинности и сценарии приложений](authentication-flows-app-scenarios.md)
