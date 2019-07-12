---
title: Веб-приложения, что вызовы веб-API, (вызов веб-API) — платформе Microsoft identity
description: Узнайте, как создать веб-приложение, которое вызывает веб-API (вызов веб-API)
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
ms.openlocfilehash: 3624f4e859081e53ee27b6f8415eb3f9b5a2a5fa
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785461"
---
# <a name="web-app-that-calls-web-apis---call-a-web-api"></a>Веб-приложение, которое вызывает веб-API - вызов веб-API

Теперь, когда у вас есть маркер, можно вызвать защищенный веб-API.

## <a name="aspnet-core"></a>ASP.NET Core

Ниже приведен упрощенный код действия `HomeController`. Этот код получает токен для вызова Microsoft Graph. Этот код времени была добавлена, показывающий, как для вызова Microsoft Graph, как REST API. URL-адрес для graph API предоставляется в `appsettings.json` файлов и чтение в переменной с именем `webOptions`:

```JSon
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;

 // Calls the web API (here the graph)
 HttpClient httpClient = new HttpClient();
 httpClient.DefaultRequestHeaders.Authorization =
     new AuthenticationHeaderValue(Constants.BearerAuthorizationScheme,accessToken);
 var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

 if (response.StatusCode == HttpStatusCode.OK)
 {
  var content = await response.Content.ReadAsStringAsync();

  dynamic me = JsonConvert.DeserializeObject(content);
  return me;
 }

 ViewData["Me"] = me;
 return View();
}
```

> [!NOTE]
> Для вызова веб-API, можно использовать тот же принцип.
>
> Наиболее Azure web API предоставляют пакет SDK, который упрощает вызов этого метода. Это также происходит из Microsoft Graph. Вы узнаете, в следующей статье где можно найти руководства, иллюстрирующие эти аспекты.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-web-app-call-api-production.md)
