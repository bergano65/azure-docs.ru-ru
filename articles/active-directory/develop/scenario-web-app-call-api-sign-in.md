---
title: Веб-приложение, вызывающее веб-API (вход) — платформа Microsoft Identity
description: Узнайте, как создать веб-приложение, вызывающее веб-API (вход)
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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3036f8cb72f2a07673743a77e8be37614002563f
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720200"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Веб-приложение, вызывающее веб-API — вход

Вы уже знакомы с добавлением единого входа в веб-приложение. Вы узнаете, что в [веб-приложении, в котором входят пользователи, — добавление входа в систему](scenario-web-app-sign-user-sign-in.md).

В этом случае, если пользователь выйдет из этого приложения или из любого приложения, вы хотите удалить его из кэша маркеров, связанных с этим пользователем маркеров.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Перехват обратного вызова после выхода — единый выход

Приложение может перехватить событие после `logout`, например, чтобы очистить запись кэша маркеров, связанную с учетной записью, для которой выполнен выход. Веб-приложение будет хранить маркеры доступа для пользователя в кэше. Перехват обратного вызова после `logout` позволяет веб-приложению удалить пользователя из кэша маркеров.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Этот механизм показан в методе `AddMsal()` метода [вебаппсервицеколлектионекстенсионс. CS # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)

**URL-адрес выхода** , зарегистрированный для приложения, позволяет реализовать единый выход. Конечная точка платформы Microsoft Identity `logout` будет вызывать **URL-адрес выхода** , зарегистрированный в приложении. Этот вызов происходит, если выход был инициирован из веб-приложения или из другого веб-приложения или браузера. Дополнительные сведения см. в разделе [единый выход](v2-protocols-oidc.md#single-sign-out).

```CSharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: removing the account from MSAL.NET cache
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

Код для Ремовеаккаунтасинк доступен в [Microsoft. Identity. Web/токенаккуиситион. CS # L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Пример ASP.NET не удаляет учетные записи из кэша при глобальном выходе

# <a name="javatabjava"></a>[Java](#tab/java)

Пример Java не удаляет учетные записи из кэша при глобальном выходе

# <a name="pythontabpython"></a>[Python](#tab/python)

Пример Python не удаляет учетные записи из кэша при глобальном выходе

---

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Получение маркера для веб-приложения](scenario-web-app-call-api-acquire-token.md)
