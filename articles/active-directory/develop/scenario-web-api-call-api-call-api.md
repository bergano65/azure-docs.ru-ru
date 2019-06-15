---
title: Веб-API, который вызывает веб-API (вызов API-интерфейсы) - платформой Microsoft identity
description: Сведения о создании веб-API, вызывающее подчиненными веб-API-интерфейсы (вызов веб-API).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cd093cc68a21558dc326221eeaa8c034c24f1c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074730"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>Веб-API, который вызывает веб-API - вызова API

Получив маркер, можно вызвать защищенный веб-API. Это делается на контроллере ASP.NET/ASP.NET Core веб-API.

## <a name="controller-code"></a>Код контроллера

Здесь является продолжением примера кода, показанного на [вызывает защищенный веб-API веб-API — получение токена](scenario-web-api-call-api-acquire-token.md), с именем действия контроллеров API, вызвав API нижнего уровня (с именем "todolist").

После получения маркера используйте как токен носителя для вызова API нижнего уровня.

```CSharp
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

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-web-api-call-api-production.md)
