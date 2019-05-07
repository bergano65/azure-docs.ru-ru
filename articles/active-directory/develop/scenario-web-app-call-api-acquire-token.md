---
title: Веб-приложение, вызывающее веб-интерфейсы API (получить маркер для приложения) — платформе Microsoft identity
description: Узнайте, как создать веб-приложение, которое вызывает веб-API (получение токена для приложения)
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
ms.openlocfilehash: 653db995000308bb3ef78a9183696cd9d8ed1056
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074805"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Веб-приложение, вызывающее веб-интерфейсы API — получение маркера для приложения

Теперь вы создали приложение клиентского объекта, используем его, чтобы получить маркер, который затем будет использоваться для вызова веб-API. В ASP.NET или ASP.NET Core вызов веб-API осуществляется в контроллере. Дело:

- Получение маркера для веб-API с помощью кэша маркера. Для этого вызовите `AcquireTokenSilent`
- Вызов API, защищенный маркером доступа

## <a name="aspnet-core"></a>ASP.NET Core;

Методы контроллера защищены с помощью `[Authorize]` атрибут, который вынуждает пользователей станут проверенными для использования веб-приложения. Ниже приведен код, который вызывает Microsoft Graph

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Ниже приведен упрощенный код действия HomeController, который получает токен для вызова Microsoft Graph.

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

Если вы хотите понять в сведениях о общее количество кода, необходимые для этого сценария, см. на этапе 2 [2-1-Web App вызовы Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph) шаге [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) учебника

Существуют многочисленные дополнительные сложности такие как:

- Реализация кэша маркера для веб-приложения (учебник представить несколько реализаций)
- Удаление учетной записи из кэша, когда пользователь выполняет масштабирование
- Вызов несколько интерфейсов API, в том числе добавочное согласие

## <a name="aspnet"></a>ASP.NET:

В ASP.NET похожи вещи:

- Действие контроллера, защищенные с помощью атрибута [Authorize] извлечет идентификатор клиента и идентификатор пользователя для `ClaimsPrincipal` член контроллера (ASP.NET использует `HttpContext.User`)
- Оттуда он создает MSAL.NET `IConfidentialClientApplication`
- Вызов ИТ `AcquireTokenSilent` метод конфиденциального клиентского приложения 

код похож на код, показанный для ASP.NET Core

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Вызов веб-API](scenario-web-app-call-api-call-api.md)
