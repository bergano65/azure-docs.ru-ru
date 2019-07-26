---
title: Мобильное приложение, вызывающее веб-API — вызов веб-API | Платформа Microsoft Identity
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
ms.openlocfilehash: 76f0cddfa889376d3795726e74d82e53417b31f1
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413574"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Мобильное приложение, вызывающее веб-API — вызов веб-API

После того как приложение подписало пользователя и получило маркеры, MSAL предоставляет несколько частей информации о пользователе, среде пользователя и токенах. Приложение может использовать эти значения для вызова веб-API или вывода приветственного сообщения пользователю.

Сначала мы рассмотрим результат MSAL. Затем мы рассмотрим, как использовать маркер доступа из `AuthenticationResult` или `result` для вызова защищенного веб-API.

## <a name="msal-result"></a>MSAL результат
MSAL предоставляет следующие значения: 

- `AccessToken`. Используется для вызова защищенных веб-API в запросе носителя HTTP.
- `IdToken`. Содержит полезные сведения о пользователе, выполнившего вход, например имя пользователя, домашний клиент и уникальный идентификатор хранилища.
- `ExpiresOn`. Срок действия маркера. MSAL обрабатывает автоматическое обновление приложений.
- `TenantId`. Идентификатор клиента, с которым пользователь вошел в. Для гостевых пользователей (Azure Active Directory B2B) это значение будет обозначать клиента, с которым пользователь вошел в систему, а не домашний клиент пользователя.  
- `Scopes`. Области, которые были предоставлены с помощью токена. Предоставленные области могут быть подмножеством запрошенных областей.

MSAL также предоставляет абстракцию для `Account`. `Account` Представляет учетную запись текущего пользователя.

- `HomeAccountIdentifier`. Идентификатор домашнего клиента пользователя.
- `UserName`. Имя пользователя, предпочитаемое пользователем. Это может быть пустым для Azure Active Directory B2C пользователей.
- `AccountIdentifier`. Идентификатор пользователя, выполнившего вход. Это значение будет таким же, как `HomeAccountIdentifier` и значение в большинстве случаев, если пользователь не является гостевым в другом клиенте.

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

### <a name="ios"></a>iOS

```swift
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)

        // Put access token in HTTP request.
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in
            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }
            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            // Successfully got data from Graph.
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="making-several-api-requests"></a>Выполнение нескольких запросов API

Если необходимо вызвать один API несколько раз или при необходимости вызвать несколько API, примите во внимание следующие моменты при сборке приложения.

- **Добавочное согласие**: Платформа Microsoft Identity позволяет приложениям получать согласие пользователя, так как требуются разрешения, а не все в начале. Каждый раз, когда приложение готово к вызову API, оно должно запрашивать только те области, которые он должен использовать.
- **Условный доступ**: В некоторых случаях при выполнении нескольких запросов API могут быть получены дополнительные требования к условному доступу. Это может произойти, если для первого запроса не применены политики условного доступа, и приложение пытается автоматически получить доступ к новому API, для которого требуется условный доступ. Для обработки этого сценария не забудьте перехватывать ошибки из запросов без вмешательства пользователя и подготовиться к выполнению интерактивного запроса.  Дополнительные сведения см. в разделе [руководство по условному доступу](conditional-access-dev-guide.md).

## <a name="calling-several-apis-in-xamarin-or-uwp---incremental-consent-and-conditional-access"></a>Вызов нескольких API в Xamarin или UWP — добавочное согласие и условный доступ

Если необходимо вызвать несколько API для одного и того же пользователя, то после получения маркера для пользователя можно избежать неоднократного запроса учетных данных `AcquireTokenSilent` , чтобы получить маркер.

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
> [Переместить в рабочую среду](scenario-mobile-production.md)
