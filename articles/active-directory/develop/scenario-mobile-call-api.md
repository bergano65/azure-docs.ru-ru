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
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd65b9f97c373c55a3486e06e83fca7cf824cad
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075120"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Мобильное приложение, которое вызывает веб-API - вызов веб-API

После ваше приложение пользователь, выполнивший вход и получения маркеров, MSAL предоставляет несколько видов информации о пользователе, среды и маркеры, выпущенные. Приложение может использовать эти значения для вызова веб-API или отображения приветственное сообщение для пользователя.

Во-первых, мы изучим MSAL результат, а затем как использовать маркер доступа из `AuthenticationResult` или `result` для вызова защищенного веб-API.

## <a name="msal-result"></a>Результат MSAL

- `AccessToken`: Используется для вызова защищенного веб-API в запросе HTTP носителя.
- `IdToken`: Содержит полезные утверждения о пользователю, выполнившему вход, как их имя, домашнем клиенте и уникальный идентификатор для хранилища.
- `ExpiresOn`: время истечения срока действия маркера. MSAL обрабатывает автоматическое обновление для приложения.
- `TenantId`: Идентификатор клиента пользователя, используемой для входа. Для гостевых пользователей (Azure AD B2B) это будет клиента пользователя, выполнившего вход при помощи, не их домашнем клиенте.  
- `Scopes`: области, которые были предоставлены с токеном. Это может быть подмножеством вы запросили.

Кроме того, MSAL также предоставляет абстракцию для `Account`. Учетной записи представляет текущего пользователя на вход в учетную запись.

- `HomeAccountIdentifier`: Идентификатор домашний клиент пользователя.
- `UserName`: Предпочтительное имя пользователя пользователя. Это может быть пустым для пользователей Azure AD B2C.
- `AccountIdentifier`: Идентификатор пользователя, выполнившего вход. Это будет таким же, как `HomeAccountIdentifier` в большинстве случаев, если пользователь является гостем в другом клиенте.

## <a name="calling-an-api"></a>Вызов API

Когда готовы маркер доступа, это просто вызывать веб-API. Приложение принимает это маркер, составлять запросы HTTP и выполнить его.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called graph, process data and send to UI 
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put Access Token in HTTP request 
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

        // Put Access token in HTTP Request
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

            // Successfully got data from Graph
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put Access token in HTTP request 
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Выполнение нескольких запросов API

Если вам нужно вызывать тот же API несколько раз или несколько API-интерфейсов, существуют дополнительные рекомендации, при создании своего приложения:

- ***Добавочное согласие***: Платформы удостоверений позволяет приложениям для получения согласия пользователя, как требуется, а не все первоначальные разрешения. Каждый раз, когда приложение будет готово для вызова API, следует запросить только области, которые планируется использовать.
- ***Условный доступ***: В некоторых сценариях вы можете получить дополнительные требования для условного доступа, при создании нескольких запросов к API. Для обработки этого сценария, убедитесь, что перехват ошибок от автоматической запросов и будьте готовы сделать интерактивный запрос. Это может произойти, если первый запрос характеризуется не были применены политики условного доступа и приложения пытается автоматически получить доступ к новый API, который требует условного доступа. Дополнительные сведения см. в разделе [рекомендации для условного доступа](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-mobile-production.md)
