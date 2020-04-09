---
title: Позвоните в веб-API из мобильного приложения Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как создать мобильное приложение, которое вызывает web-апоттестаты. (Позвоните в веб-API.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 28f57c5657ce2f8537a654a7f67ed4481fab2c91
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882698"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Вызов веб-API из мобильного приложения

После того, как приложение подписывает сяпользователя и получает токены, Библиотека подлинности Майкрософт (MSAL) предоставляет информацию о пользователе, среде пользователя и выпущенных токенах. Приложение может использовать эти значения для вызова веб-API или отображения приветственного сообщения пользователю.

В этой статье мы сначала рассмотрим результат MSAL. Затем мы рассмотрим, как использовать токен `AuthenticationResult` `result` доступа или вызвать защищенный веб-API.

## <a name="msal-result"></a>Результат MSAL
MSAL предоставляет следующие значения: 

- `AccessToken`вызовы защищенных web-aIS в запросе на предъявителя HTTP.
- `IdToken`содержит полезную информацию о входе в пользователя. Эта информация включает в себя имя пользователя, арендатора дома и уникальный идентификатор для хранения.
- `ExpiresOn`является временем истечения токена. MSAL обрабатывает автоматическое обновление приложения.
- `TenantId`является идентификатором арендатора, в котором пользователь вписался. Для гостевых пользователей в B2B Active Directory (Azure AD) это значение определяет арендатора, в который внедён пользователь. Значение не определяет жильца дома пользователя.  
- `Scopes`указывает области, которые были предоставлены с маркером. Предоставленные области могут быть подмножеством запрошенных областей.

MSAL также предоставляет абстракцию для `Account` значения. Значение `Account` представляет учетную запись текущего пользователя:

- `HomeAccountIdentifier`идентифицирует жильца дома пользователя.
- `UserName`является предпочтительным именем пользователя. Это значение может быть пустым для пользователей Azure AD B2C.
- `AccountIdentifier`идентифицирует пользователя, вписаваемого в нее. В большинстве случаев это значение `HomeAccountIdentifier` такое же, как и значение, если пользователь не является гостем другого арендатора.

## <a name="call-an-api"></a>Вызов API

После того, как у вас есть токен доступа, вы можете вызвать web API. Приложение будет использовать маркер для создания запроса HTTP, а затем для выполнения запроса.

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

Методы приобретения токенов возвращают `MSALResult` объект. `MSALResult`разоблачает свойство. `accessToken` Можно использовать `accessToken` для вызова веб-API. Добавьте это свойство в заголовок авторизации HTTP, прежде чем звонить, чтобы получить доступ к защищенного web API.

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

## <a name="make-several-api-requests"></a>Сделайте несколько запросов API

Если вам нужно вызвать один и тот же API несколько раз, или если вам нужно вызвать несколько API, следует учитывать следующие темы при создании приложения:

- **Инкрементное согласие**: Платформа идентификации Майкрософт позволяет приложениям получать согласие пользователя, когда требуется разрешение, а не все в начале. Каждый раз, когда приложение готово вызывать API, оно должно запрашивать только те области, которые ему нужны.

- **Условный доступ**: При выполнении нескольких запросов API в определенных сценариях может потребоваться выполнить дополнительные требования к условному доступу. Требования могут увеличиваться таким образом, если первый запрос не имеет политикусловного доступа и приложение пытается бесшумно получить доступ к новому API, который требует условного доступа. Чтобы справиться с этой проблемой, не забудьте поймать ошибки из молчаливых запросов, и будьте готовы сделать интерактивный запрос.  Для получения дополнительной информации [см.](../azuread-dev/conditional-access-dev-guide.md)

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Позвоните в несколько AA, используя поэтапное согласие и условный доступ

Если вам нужно позвонить несколько AA для одного и того же пользователя, после приобретения токена `AcquireTokenSilent` для пользователя, вы можете избежать повторного запроса у пользователя учетных данных, позвонив впоследствии, чтобы получить токен:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Взаимодействие необходимо, когда:

- Пользователь согласился на первый API, но теперь необходимо дать согласие на дополнительные области. В этом случае вы используете инкрементное согласие.
- Первый API не требует многофакторной аутентификации, но следующий API делает.

```csharp
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
> [Перенос в рабочую среду](scenario-mobile-production.md)
