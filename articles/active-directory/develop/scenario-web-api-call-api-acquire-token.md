---
title: Веб-API, веб-вызовы других API-интерфейсы (получить маркер для приложения) — платформе Microsoft identity
description: Сведения о создании веб-API, что вызовы других веб-API, (получение токена для приложения).
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
ms.openlocfilehash: 986e2e0f8a481d61dc870af2548290658b44d2d3
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231098"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>Веб-API, вызывающее веб-интерфейсы API — получение маркера для приложения

После создания клиентского приложения, используйте его, чтобы получить маркер, который можно использовать для вызова веб-API.

## <a name="code-in-the-controller"></a>Код в контроллер

Ниже приведен пример кода, который будет вызываться в действия контроллеров API, вызвав API нижнего уровня (с именем "todolist").

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

`BuildConfidentialClient()` похоже, что вы уже видели в этой статье [веб-API, который вызывает веб-API — Конфигурация приложения](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` Создает экземпляр `IConfidentialClientApplication` с кэшем, который содержит сведения только для одной учетной записи. Учетная запись предоставляется `GetAccountIdentifier` метод.

`GetAccountIdentifier` Метод использует утверждения, связанные с удостоверением, для которого веб-API полученный маркер JWT пользователем:

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
