---
title: Удалить учетные записи из кэша маркеров при выходе из системы регистрации - платформа идентификации Майкрософт Azure
description: Узнайте, как удалить учетную запись из кэша маркеров при выходе
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 934b756329065c466f21fca1480247065bdea28b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881618"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Веб-приложение, которое вызывает web-aI: Удалите учетные записи из кэша маркеров в глобальном вывеске

Вы узнали, как добавить в свой веб-приложение в [веб-приложении, которое регистрируется в пользователях: Войти в систему и войти в систему](scenario-web-app-sign-user-sign-in.md).

Регистрация отличается для веб-приложения, которое вызывает веб-апи. Когда пользователь выходит из приложения или из какого-либо приложения, необходимо удалить маркеры, связанные с этим пользователем, из кэша маркеров.

## <a name="intercept-the-callback-after-single-sign-out"></a>Перехват обратного вызова после одного выхода

Чтобы очистить запись кэша маркеров, связанную с заданным счетом, приложение может перехватить после `logout` событий. Веб-приложения хранят токены доступа для каждого пользователя в кэше маркеров. Перехватив после `logout` обратный вызов, веб-приложение может удалить пользователя из кэша.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Для ASP.NET Core механизм перехвата `AddMsal()` проиллюстрирован в методе [WebAppServiceCollectionCollectionExtensions.cs-L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157).

URL-адрес Logout, который вы ранее зарегистрировали для приложения, позволяет реализовать одиночный выход. Конечная точка `logout` платформы Майкрософт вызывает ваш URL-адрес Logout. Этот вызов происходит, если выход начался с вашего веб-приложения, или из другого веб-приложения или браузера. Для получения дополнительной информации [см.](v2-protocols-oidc.md#single-sign-out)

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: Remove the account from MSAL.NET cache.
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache.
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

Код можно `RemoveAccountAsync` получить на сайте [Microsoft.Identity.Web/TokenAcquisition.cs-L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Образец ASP.NET не удаляет учетные записи из кэша на глобальном вывески.

# <a name="java"></a>[Java](#tab/java)

Образец Java не удаляет учетные записи из кэша на глобальном вывески.

# <a name="python"></a>[Python](#tab/python)

Образец Python не удаляет учетные записи из кэша на глобальном вывески.

---

## <a name="next-steps"></a>Дальнейшие действия

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Приобретите токен для веб-приложения](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Приобретите токен для веб-приложения](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Приобретите токен для веб-приложения](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Приобретите токен для веб-приложения](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
