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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6063d143e2f217426bdf1db217fde46f8542d314
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965217"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>Веб-API, вызывающий веб-API — вызов API

После получения маркера можно вызвать защищенный веб-API. Это делается с контроллера веб-API ASP.NET/ASP.NET Core.

## <a name="controller-code"></a>Код контроллера

Вот продолжение примера кода, показанного в разделе [защищенный веб-API, вызывает веб-API — получение маркера](scenario-web-api-call-api-acquire-token.md), вызываемого в действиях контроллеров API, вызов подчиненного API (с именем ToDoList).

После получения маркера используйте его в качестве токена носителя для вызова подчиненного API.

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
