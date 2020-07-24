---
title: Веб-API, вызывающий веб-API — платформа Microsoft Identity | Службы
description: Узнайте, как создать веб-API, который вызывает веб-API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b756d7df03bd3c06b703617dbf84a194d716f1e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026379"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Веб-API, вызывающий веб-API: вызов API

После создания маркера можно вызвать защищенный веб-API. Это можно сделать с помощью контроллера веб-API.

## <a name="controller-code"></a>Код контроллера

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Следующий код продолжит пример кода, показанного в [веб-API, который вызывает веб-API: получение маркера для приложения](scenario-web-api-call-api-acquire-token.md). Код вызывается в действиях контроллеров API. Он вызывает нисходящий API с именем *ToDoList*.

После получения маркера используйте его в качестве токена носителя для вызова подчиненного API.

```csharp
private async Task CallTodoListService(string accessToken)
{

// After the token has been returned by Microsoft Identity Web, add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

# <a name="java"></a>[Java](#tab/java)

Следующий код продолжит пример кода, показанного в [веб-API, который вызывает веб-API: получение маркера для приложения](scenario-web-api-call-api-acquire-token.md). Код вызывается в действиях контроллеров API. Он вызывает нисходящий API MS Graph.

После получения маркера используйте его в качестве токена носителя для вызова подчиненного API.

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
Пример, демонстрирующий этот поток с помощью MSAL Python, пока недоступен.

---

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Веб-API, который вызывает веб-API: переместить в рабочую среду](scenario-web-api-call-api-production.md)
