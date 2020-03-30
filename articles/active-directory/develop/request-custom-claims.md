---
title: Запрос пользовательских требований (MSAL iOS/macOS) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как запрашивать пользовательские требования.
services: active-directory
documentationcenter: ''
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 44158296faaf238fd72f2360149d3d93f68c5ba0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085607"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Как: Запрос пользовательских претензий с помощью MSAL для iOS и macOS

OpenID Connect позволяет дополнительно запрашивать возврат индивидуальных требований из конечной точки UserInfo и/или в токене ID. Запрос претензий представлен как объект JSON, содержащий список запрошенных претензий. Для получения более подробной информации можно ознакомиться с [OpenID Connect Core 1.0.](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter)

Библиотека аутентификации Майкрософт (MSAL) для iOS и macOS позволяет запрашивать конкретные претензии как в интерактивных, так и в бесшумных сценариях приобретения токенов. Он делает это `claimsRequest` через параметр.

Существует несколько сценариев, в которых это необходимо. Пример:

- Запрос претензий за пределами стандартного набора для приложения.
- Запрос конкретных комбинаций стандартных утверждений, которые не могут быть указаны с использованием областей для приложения. Например, если токен доступа получает отклонен из-за отсутствия претензий, приложение может запросить недостающие претензии с помощью MSAL.

> [!NOTE]
> MSAL обходит кэш маркера доступа всякий раз, когда указан запрос претензий. Важно предоставлять `claimsRequest` параметр только при необходимости дополнительных требований `claimsRequest` (в отличие от всегда обеспечивающего один и тот же параметр в каждом вызове MSAL API).

`claimsRequest`могут быть `MSALSilentTokenParameters` указаны в и: `MSALInteractiveTokenParameters`

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest`может быть построен из NSString представительства запроса претензий JSON. 

Objective-C.

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



Он также может быть изменен, запрашивая дополнительные конкретные претензии:

Objective-C.

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift:

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest`затем должны быть установлены в параметрах токена и предоставлены одному из AIS по приобретению токенов MSAL:

Objective-C.

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в [Потоки проверки подлинности и сценарии приложений](authentication-flows-app-scenarios.md)
