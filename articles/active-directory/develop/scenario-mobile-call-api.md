---
title: Мобильное приложение, что вызовы веб-API - вызов веб-API | Платформы удостоверений Microsoft
description: Узнайте, как создать мобильное приложение, которое вызывает веб-API (вызов веб-API)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
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
ms.openlocfilehash: 7fc8c21db0f42bbb6804c00e27e82f840d7038c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111174"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Мобильное приложение, которое вызывает веб-API - вызов веб-API

Приложение пользователь, выполнивший вход, полученных маркеров MSAL предоставляет несколько видов информации о пользователе, среду и маркеры, выпущенные. Приложение может использовать эти значения для вызова веб-API или отображать приветственное сообщение для пользователя.

Во-первых мы рассмотрим результат MSAL. Затем мы рассмотрим способы использования маркера доступа из `AuthenticationResult` или `result` для вызова защищенного веб-API.

## <a name="msal-result"></a>Результат MSAL
MSAL предоставляет следующие значения: 

- `AccessToken`: Используется для вызова защищенного веб-API в запросе HTTP носителя.
- `IdToken`: Содержит полезную информацию о выполнившего вход пользователя в виде имени пользователя, домашний клиент и уникальный идентификатор хранилища.
- `ExpiresOn`: Время окончания срока действия маркера. MSAL обрабатывает автоматическое обновление для приложения.
- `TenantId`: Идентификатор клиента, который пользователь вошел в систему. Для гостевых пользователей (Azure Active Directory B2B) это значение будет идентифицировать клиента, который пользователю войти, не домашний клиент пользователя.  
- `Scopes`: Области, которые были предоставлены с токеном. Предоставленный области может быть подмножеством области, которые вы запросили.

MSAL также предоставляет абстракцию для `Account`. `Account` Представляет текущего пользователя, вошедшего в систему учетной записи.

- `HomeAccountIdentifier`: Идентификатор домашний клиент пользователя.
- `UserName`: Имя пользователя основной пользователь. Это может быть пустым для пользователей Azure Active Directory B2C.
- `AccountIdentifier`: Идентификатор пользователя, выполнившего вход. Это значение будет таким же, как `HomeAccountIdentifier` значение в большинстве случаев, если пользователь является гостем в другом клиенте.

## <a name="call-an-api"></a>Вызов API

После получения маркера доступа, достаточно вызвать веб-API. Приложение будет использовать токен для создания HTTP-запроса, а затем выполните запрос.

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

```CSharp
httpClient = new HttpClient();

// Put access token in HTTP request.
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Выполнение нескольких запросов API

Если вам нужно вызывать тот же API несколько раз, или, необходимые для вызова нескольких API, выполните следующие факторы при сборке приложения.

- **Добавочное согласие**: Платформы удостоверений Microsoft позволяет приложениям запрашивать его согласие пользователя как требуются разрешения, а не все в начале. Каждый раз, когда приложение будет готово для вызова API, следует запросить только области, из которых необходимо возвратить.
- **Условный доступ**: В некоторых случаях можно получить дополнительные требования для условного доступа при внесении нескольких запросов API. Это может произойти, если первый запрос характеризуется не были применены политики условного доступа и приложения пытается автоматически получить доступ к новый API, который требует условного доступа. Для обработки этого сценария, убедитесь, что перехват ошибок от автоматической запросов и будьте готовы сделать интерактивный запрос.  Дополнительные сведения см. в разделе [рекомендации для условного доступа](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-mobile-production.md)
