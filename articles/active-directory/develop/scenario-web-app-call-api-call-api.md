---
title: Вызов веб-API из веб-приложения — платформа Microsoft Identity | Службы
description: Узнайте, как создать веб-приложение, вызывающее веб-API (вызов защищенного веб-API).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 12802ab6dcfbbe5a1c5576ab672ead864dd0b4ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82559879"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Веб-приложение, вызывающее веб-API: вызов веб-API

Теперь, когда у вас есть маркер, можно вызвать защищенный веб-API.

## <a name="call-a-protected-web-api"></a>Вызов защищенного веб-API

Вызов защищенного веб-API зависит от выбранного языка и платформы.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Ниже приведен упрощенный код для действия `HomeController` . Этот код возвращает маркер для вызова Microsoft Graph. Добавлен код, демонстрирующий вызов Microsoft Graph как REST API. URL-адрес для Microsoft Graph API предоставляется в appsettings.jsв файле и считывается в переменной с именем `webOptions` :

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 
  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```

> [!NOTE]
> Вы можете использовать тот же принцип для вызова любого веб-API.
>
> Большинство веб-API Azure предоставляют пакет SDK, упрощающий вызов API. Это также справедливо для Microsoft Graph. В следующей статье вы узнаете, где найти учебник, демонстрирующий использование API.

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

    // Set the appropriate header fields in the request header.
    conn.setRequestProperty("Authorization", "Bearer " + accessToken);
    conn.setRequestProperty("Accept", "application/json");

    String response = HttpClientHelper.getResponseStringFromConn(conn);

    int responseCode = conn.getResponseCode();
    if(responseCode != HttpURLConnection.HTTP_OK) {
        throw new IOException(response);
    }

    JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Перенос в рабочую среду](scenario-web-app-call-api-production.md)
