---
title: Классическое приложение, вызывающее веб-API (вызов веб-API) — платформа Microsoft Identity
description: Узнайте, как создать классическое приложение, вызывающее веб-API (вызов веб-API).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56d3d01e39adfeb6bf2ef5e7e7d595f49c90f5a5
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268281"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Классическое приложение, вызывающее веб-API — вызов веб-API

Теперь, когда у вас есть маркер, можно вызвать защищенный веб-API.

## <a name="calling-a-web-api-from-net"></a>Вызов веб-API из .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-a-web-api-in-msal-for-ios-and-macos"></a>Вызов веб-API в MSAL для iOS и macOS

Методы получения маркеров возвращают `MSALResult` объект. `MSALResult``accessToken` предоставляет свойство, которое можно использовать для вызова веб-API. Маркер доступа должен быть добавлен в заголовок авторизации HTTP перед вызовом доступа к защищенному веб-API.

Objective-C.

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

SWIFT

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Вызов нескольких интерфейсов API — добавочное согласие и условный доступ

Если необходимо вызвать несколько API для одного и того же пользователя, то после получения маркера для первого API можно просто вызвать `AcquireTokenSilent`, и вы получите маркер для других интерфейсов API в большинстве случаев.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Случаи, когда требуется взаимодействие,:

- Пользователь, которому предоставлен доступ к первому API, теперь должен предоставить согласие на дополнительные области (последовательное согласие)
- Первый API не требует многофакторной проверки подлинности, а следующий —.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-desktop-production.md)
