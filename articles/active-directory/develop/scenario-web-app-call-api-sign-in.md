---
title: Веб-приложения, что вызовы веб-API-интерфейсы (вход в систему) — платформе Microsoft identity
description: Узнайте, как создать веб-приложение, которое вызывает веб-API (вход)
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
ms.openlocfilehash: 663cea72eb620217ad5fa8925d3bb00eedbf890c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074565"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Веб-приложение, вызывающее веб-интерфейсы API — вход

Вы уже умеете Добавление функции входа в веб-приложения. Вы узнаете, что в [веб-приложения, что входит в пользователи - Добавление функции входа в](scenario-web-app-sign-user-sign-in.md).

Что здесь отличается, что когда пользователь выполнил выход из этого приложения и из любого приложения, нужно удалить из кэша токенов, токены, связанные с пользователем.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Перехват обратный вызов после выхода - единого выхода

Приложение может перехватывать после `logout` событий, например для очистки записи кэша маркера, связанный с учетной записью, выхода из системы. Мы увидим во второй части этого руководства (о веб-приложении, вызвав веб-API), что веб-приложения будут храниться в кэше маркеров доступа для пользователя. Перехват после `logout` обратный вызов позволяет веб-приложения удалить пользователя из кэша маркера. Этот механизм проиллюстрирован на `AddMsal()` метод [StartupHelper.cs L137 143](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L137-L143)

**URL-адрес выхода** , зарегистрированное для вашего приложения вы можете реализовать единый выход. Платформе Microsoft identity `logout` вызовет конечную точку **URL-адрес выхода** зарегистрировано приложение. Этот вызов выполняется, если выход был инициировано из веб-приложения или из браузера или другой веб-приложения. Дополнительные сведения см. в разделе [единого выхода](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc#single-sign-out) в основной документации.

```CSharp
public static IServiceCollection AddMsal(this IServiceCollection services, IEnumerable<string> initialScopes)
{
    services.AddTokenAcquisition();

    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
     ...
        // Handling the sign-out: removing the account from MSAL.NET cache
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            // Remove the account from MSAL.NET token cache
            var _tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
            await _tokenAcquisition.RemoveAccount(context);
        };
    });
    return services;
}
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Получение токена для веб-приложения](scenario-web-app-call-api-acquire-token.md)
