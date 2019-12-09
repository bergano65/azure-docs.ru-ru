---
title: Получение маркера для веб-API, который вызывает веб-API | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как создать веб-API, который вызывает веб-API, требующие получения маркера для приложения.
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
ms.openlocfilehash: 276ff1e5e9f709aa5b38d1efa4055dfe3baf3cc5
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919789"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>Веб-API, вызывающий веб-API — получение маркера для приложения

После построения объекта клиентского приложения используйте его для получения маркера, который можно использовать для вызова веб-API.

## <a name="code-in-the-controller"></a>Код в контроллере

Ниже приведен пример кода, который будет вызываться в действиях контроллеров API с вызовом подчиненного API (с именем ToDoList).

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
}
```

`BuildConfidentialClient()` аналогично тому, что вы видели в [веб-API статьи, который вызывает веб-API — конфигурацию приложения](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` создает экземпляры `IConfidentialClientApplication` с кэшем, который содержит только сведения для одной учетной записи. Учетная запись предоставляется методом `GetAccountIdentifier`.

Метод `GetAccountIdentifier` использует утверждения, связанные с идентификатором пользователя, для которого веб-API получил JWT:

```CSharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Вызов веб-API](scenario-web-api-call-api-call-api.md)
