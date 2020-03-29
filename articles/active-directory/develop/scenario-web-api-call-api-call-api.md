---
title: Веб-aPI, который вызывает веб-API - платформа идентификации Microsoft (ru) Azure
description: Узнайте, как создать веб-aPI, который вызывает web-aPI.
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
ms.openlocfilehash: d66a08d4e84a3771d6c3fa46b96c975869435452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76833385"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Веб-aPI, который вызывает веб-API: Вызов API

После того, как у вас есть маркер, вы можете вызвать защищенный веб-API. Это делается с контроллера веб-API.

## <a name="controller-code"></a>Код контроллера

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Следующий код продолжает пример кода, отображаемого в [веб-aPI, который вызывает web API: Приобрести маркер для приложения.](scenario-web-api-call-api-acquire-token.md) Код вызывается в действиях контроллеров API. Он вызывает ниже по течению API с именем *todolist*.

После приобретения токена используйте его в качестве маркера на предъявителя для вызова API ниже по течению.

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...

// After the token has been returned by Microsoft Authentication Library (MSAL), add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

# <a name="java"></a>[Java](#tab/java)

Следующий код продолжает пример кода, отображаемого в [веб-aPI, который вызывает web API: Приобрести маркер для приложения.](scenario-web-api-call-api-acquire-token.md) Код вызывается в действиях контроллеров API. Он вызывает вниз по течению API MS Graph.

После приобретения токена используйте его в качестве маркера на предъявителя для вызова API ниже по течению.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Образец, демонстрирующий этот поток с помощью MSAL Python, пока недоступен.

---

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Веб-aPI, который вызывает веб-API: Переход к производству](scenario-web-api-call-api-production.md)
