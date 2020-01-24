---
title: Веб-API, вызывающий веб-API — платформа Microsoft Identity | Службы
description: Узнайте, как создать веб-API, который вызывает веб-API.
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
ms.openlocfilehash: c7c54c7c4718cea479a812574e961ef2338a6be6
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701762"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Веб-API, вызывающий веб-API: вызов API

После создания маркера можно вызвать защищенный веб-API. Это можно сделать с помощью контроллера веб-API ASP.NET или ASP.NET Core.

## <a name="controller-code"></a>Код контроллера

Следующий код продолжит пример кода, показанного в [веб-API, который вызывает веб-API: получение маркера для приложения](scenario-web-api-call-api-acquire-token.md). Код вызывается в действиях контроллеров API. Он вызывает нисходящий API с именем *ToDoList*.

После получения маркера используйте его в качестве токена носителя для вызова подчиненного API.

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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Веб-API, который вызывает веб-API: переместить в рабочую среду](scenario-web-api-call-api-production.md)
