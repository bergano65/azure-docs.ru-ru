---
title: Получите токен для веб-API, который вызывает веб-API Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как создать веб-aPI, который вызывает web-aPI, требующие приобретения маркера для приложения.
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
ms.openlocfilehash: 79f8eb9e804502a7c0e61c18e4998fa05db10278
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885146"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Веб-aPI, который вызывает веб-API: Приобрести маркер для приложения

После того как вы создали объект клиентского приложения, используйте его для приобретения маркера, который можно использовать для вызова web-API.

## <a name="code-in-the-controller"></a>Код в контроллере

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Вот пример кода, который называется в действиях контроллеров API. Он вызывает ниже по течению API с именем *todolist*.

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

`BuildConfidentialClient()`похож на сценарий в [веб-aPI, который вызывает веб-API: конфигурация приложения](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()`мгновенно `IConfidentialClientApplication` с помощью кэша, содержащего информацию только для одной учетной записи. Учетная запись предоставляется `GetAccountIdentifier` методом.

Метод `GetAccountIdentifier` использует утверждения, связанные с личностью пользователя, для которого веб-aPI получил jSON Web Token (JWT):

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

# <a name="java"></a>[Java](#tab/java)
Вот пример кода, который называется в действиях контроллеров API. Он вызывает вниз по течению API - Microsoft Graph.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

# <a name="python"></a>[Python](#tab/python)

Веб-API Python должен будет использовать некоторые промежуточное программное обеспечение для проверки маркера предъявителя, полученного от клиента. Веб-API может получить токен доступа для ниже по течению API с помощью библиотеки MSAL Python, позвонив в [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) метод. Образец, демонстрирующий этот поток с помощью MSAL Python, пока недоступен.

---

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Веб-aPI, который вызывает веб-API: Вызов API](scenario-web-api-call-api-call-api.md)
