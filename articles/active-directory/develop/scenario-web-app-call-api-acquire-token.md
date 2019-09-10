---
title: Веб-приложение, вызывающее веб-API (получение маркера для приложения) — платформа Microsoft Identity
description: Узнайте, как создать веб-приложение, вызывающее веб-API (получение маркера для приложения).
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
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3aa144c76fb0a8e479658efdb5d43361fbbc085c
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860624"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Веб-приложение, вызывающее веб-API — получение маркера для приложения

Теперь, когда вы создали клиентский объект приложения, вы будете использовать его для получения маркера для вызова веб-API. В ASP.NET или ASP.NET Core вызов веб-API выполняется в контроллере. Это примерно так:

- Получение маркера для веб-API с помощью кэша маркеров. Чтобы получить этот маркер, вызовите `AcquireTokenSilent`.
- Вызов защищенного API с помощью маркера доступа.

## <a name="aspnet-core"></a>ASP.NET Core

Методы контроллера защищены `[Authorize]` атрибутом, который заставляет пользователей проходить проверку подлинности для использования веб-приложения. Ниже приведен код, который вызывает Microsoft Graph.

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Ниже приведен упрощенный код действия HomeController, который получает маркер для вызова Microsoft Graph.

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
 ...
 // use the access token to call a web API
}
```

Чтобы лучше понять код, необходимый для этого сценария, см. шаг 2 2-1 ([вызов веб-приложения Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) в учебнике [MS-Identity-aspnetcore-webapp-Tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Существует множество дополнительных сложностей, таких как:

- Реализация кэша маркеров для веб-приложения (учебник представляет несколько реализаций)
- Удаление учетной записи из кэша при выходе пользователя
- Вызов нескольких API, включая последовательное согласие

## <a name="aspnet"></a>ASP.NET

Они похожи в ASP.NET:

- Действие контроллера, защищенное атрибутом [авторизовать], извлекает идентификатор клиента и идентификатор `ClaimsPrincipal` пользователя для члена контроллера. (ASP.NET использует `HttpContext.User`.)
- После этого он создает MSAL.NET `IConfidentialClientApplication`.
- Наконец, он вызывает `AcquireTokenSilent` метод конфиденциального клиентского приложения.

Код похож на код, показанный для ASP.NET Core.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Вызов веб-API](scenario-web-app-call-api-call-api.md)
