---
title: Вызов веб-API из мобильного приложения | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как создать мобильное приложение, вызывающее веб-API (вызов веб-API).
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
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: db58f94501590eb3150700d282377ec1b2378cea
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962548"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Мобильное приложение, вызывающее веб-API — вызов веб-API

После того как приложение подписало пользователя и получило маркеры, MSAL предоставляет несколько частей информации о пользователе, среде пользователя и токенах. Приложение может использовать эти значения для вызова веб-API или вывода приветственного сообщения пользователю.

Сначала мы рассмотрим результат MSAL. Затем мы рассмотрим, как использовать маркер доступа из `AuthenticationResult` или `result` для вызова защищенного веб-API.

## <a name="msal-result"></a>MSAL результат
MSAL предоставляет следующие значения: 

- `AccessToken`: используется для вызова защищенных веб-API в запросе носителя HTTP.
- `IdToken`: содержит полезные сведения о пользователе, выполнившего вход, например имя пользователя, домашний клиент и уникальный идентификатор хранилища.
- `ExpiresOn`— срок действия маркера. MSAL обрабатывает автоматическое обновление приложений.
- `TenantId`: идентификатор клиента, с которым пользователь вошел в. Для гостевых пользователей (Azure Active Directory B2B) это значение будет обозначать клиента, с которым пользователь вошел в систему, а не домашний клиент пользователя.  
- `Scopes`: области, которые были предоставлены с помощью токена. Предоставленные области могут быть подмножеством запрошенных областей.

MSAL также предоставляет абстракцию для `Account`. `Account` представляет учетную запись текущего пользователя.

- `HomeAccountIdentifier`: идентификатор домашнего клиента пользователя.
- `UserName`: имя пользователя, предпочитаемое пользователем. Это может быть пустым для Azure Active Directory B2C пользователей.
- `AccountIdentifier`: идентификатор пользователя, выполнившего вход. Это значение будет таким же, как и значение `HomeAccountIdentifier` в большинстве случаев, если пользователь не является гостевым в другом клиенте.

## <a name="call-an-api"></a>Вызов API

После получения маркера доступа можно легко вызвать веб-API. Приложение будет использовать маркер для создания HTTP-запроса, а затем выполнить запрос.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>MSAL для iOS и MacOS

Методы получения маркеров возвращают объект `MSALResult`. `MSALResult` предоставляет свойство `accessToken`, которое можно использовать для вызова веб-API. Маркер доступа должен быть добавлен в заголовок авторизации HTTP перед вызовом доступа к защищенному веб-API.

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

Swift:

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="making-several-api-requests"></a>Выполнение нескольких запросов API

Если необходимо вызвать один API несколько раз или при необходимости вызвать несколько API, примите во внимание следующие моменты при сборке приложения.

- **Добавочное согласие**. платформа Microsoft Identity позволяет приложениям получать согласие пользователя, так как требуются разрешения, а не все в начале. Каждый раз, когда приложение готово к вызову API, оно должно запрашивать только те области, которые он должен использовать.
- **Условный доступ**. в некоторых сценариях при выполнении нескольких запросов API могут быть получены дополнительные требования к условному доступу. Это может произойти, если для первого запроса не применены политики условного доступа, и приложение пытается автоматически получить доступ к новому API, для которого требуется условный доступ. Для обработки этого сценария не забудьте перехватывать ошибки из запросов без вмешательства пользователя и подготовиться к выполнению интерактивного запроса.  Дополнительные сведения см. в разделе [руководство по условному доступу](conditional-access-dev-guide.md).

## <a name="calling-several-apis-in-xamarin-or-uwp---incremental-consent-and-conditional-access"></a>Вызов нескольких API в Xamarin или UWP — добавочное согласие и условный доступ

Если необходимо вызвать несколько API для одного и того же пользователя, то после получения маркера для пользователя вы можете избежать неоднократного запроса учетных данных, вызвав `AcquireTokenSilent`, чтобы получить маркер.

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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-mobile-production.md)
