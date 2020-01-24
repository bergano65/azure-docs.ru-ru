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
ms.openlocfilehash: 1c3d6f8d47152d70bdeaabbbc6d7b81187291857
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701813"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Веб-API, вызывающий веб-API: получение маркера для приложения

После построения объекта клиентского приложения используйте его для получения маркера, который можно использовать для вызова веб-API.

## <a name="code-in-the-controller"></a>Код в контроллере

Ниже приведен пример кода, который вызывается в действиях контроллеров API. Он вызывает нисходящий API с именем *ToDoList*.

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
}
```

`BuildConfidentialClient()` похожа на сценарий в [веб-API, который вызывает веб-API: Конфигурация приложения](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` создает экземпляр `IConfidentialClientApplication` с кэшем, который содержит сведения только для одной учетной записи. Учетная запись предоставляется методом `GetAccountIdentifier`.

Метод `GetAccountIdentifier` использует утверждения, связанные с идентификатором пользователя, для которого веб-API получил JSON Web Token (JWT):

```csharp
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
> [Веб-API, вызывающий веб-API: вызов API](scenario-web-api-call-api-call-api.md)
