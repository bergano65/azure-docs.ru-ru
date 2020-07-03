---
title: Вызов веб-API из мобильного приложения | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как создать мобильное приложение, вызывающее веб-API. (Вызов веб-API.)
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882698"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Вызов веб-API из мобильного приложения

После того как приложение подписывается на пользователя и получает маркеры, Библиотека проверки подлинности (Майкрософт) (MSAL) предоставляет сведения о пользователе, среде пользователя и выданных маркерах. Приложение может использовать эти значения для вызова веб-API или вывода приветственного сообщения пользователю.

В этой статье мы сначала рассмотрим результат MSAL. Затем мы рассмотрим, как использовать маркер доступа из `AuthenticationResult` или `result` для вызова защищенного веб-API.

## <a name="msal-result"></a>MSAL результат
MSAL предоставляет следующие значения: 

- `AccessToken`вызывает защищенные веб-API в запросе носителя HTTP.
- `IdToken`содержит полезную информацию о пользователе, выполнившего вход. Эти сведения включают имя пользователя, домашнего клиента и уникальный идентификатор хранилища.
- `ExpiresOn`срок действия маркера. MSAL обрабатывает автоматическое обновление приложения.
- `TenantId`Идентификатор клиента, выполнившего вход пользователя. Для гостевых пользователей в Azure Active Directory (Azure AD) B2B это значение идентифицирует клиента, в котором пользователь вошел в систему. Значение не определяет домашнего клиента пользователя.  
- `Scopes`Указывает области, которые были предоставлены в токене. Предоставленные области могут быть подмножеством запрошенных областей.

MSAL также предоставляет абстракцию для `Account` значения. `Account` Значение представляет учетную запись текущего пользователя, выполнившего вход.

- `HomeAccountIdentifier`Идентифицирует домашнего клиента пользователя.
- `UserName`Предпочитаемое имя пользователя. Это значение может быть пустым для Azure AD B2C пользователей.
- `AccountIdentifier`Идентифицирует пользователя, выполнившего вход. В большинстве случаев это значение совпадает со `HomeAccountIdentifier` значением, если пользователь не является гостевым в другом клиенте.

## <a name="call-an-api"></a>Вызов API

После получения маркера доступа можно вызвать веб-API. Приложение будет использовать маркер для создания HTTP-запроса, а затем выполнить запрос.

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

Методы получения маркеров возвращают `MSALResult` объект. `MSALResult`предоставляет `accessToken` свойство. Для вызова веб `accessToken` -API можно использовать. Добавьте это свойство в заголовок авторизации HTTP перед вызовом для доступа к защищенному веб-API.

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

## <a name="make-several-api-requests"></a>Создание нескольких запросов API

Если необходимо вызвать один API несколько раз или необходимо вызвать несколько API, при создании приложения учитывайте следующие моменты:

- **Добавочное согласие**. платформа Microsoft Identity позволяет приложениям получать согласие пользователя, когда требуются разрешения, а не все в начале. Каждый раз, когда приложение готово к вызову API, оно должно запрашивать только необходимые области.

- **Условный доступ**. при выполнении нескольких запросов API в некоторых сценариях может потребоваться выполнить дополнительные требования к условному доступу. Требования могут увеличиваться таким образом, если первый запрос не имеет политик условного доступа, и ваше приложение пытается автоматически получить доступ к новому API, для которого требуется условный доступ. Для решения этой проблемы следует перехватывать ошибки, возникающие при автоматическом запросе, и подготовиться к выполнению интерактивного запроса.  Дополнительные сведения см. в разделе [руководство по условному доступу](../azuread-dev/conditional-access-dev-guide.md).

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Вызов нескольких API с помощью добавочного согласия и условного доступа

Если необходимо вызвать несколько API для одного пользователя, после получения маркера для пользователя можно избежать неоднократного запроса учетных данных `AcquireTokenSilent` для получения маркера.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Взаимодействие требуется, если:

- Пользователь, которому предоставлен доступ к первому API, теперь должен предоставить согласие на дополнительные области. В этом случае используется добавочное согласие.
- Первый API не требует многофакторной проверки подлинности, а следующий API выполняет.

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
