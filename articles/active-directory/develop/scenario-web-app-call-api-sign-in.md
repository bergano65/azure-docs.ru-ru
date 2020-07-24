---
title: Удаление учетных записей из кэша маркеров при выходе — платформа Microsoft Identity | Службы
description: Узнайте, как удалить учетную запись из кэша маркеров при выходе
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: be8208c7b2b67610f8d579b1bc0094bf09b3033e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026294"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Веб-приложение, вызывающее веб-API: удаление учетных записей из кэша маркеров при глобальном выходе

Вы узнали, как добавить вход в веб-приложение в [веб-приложении, которое входит в систему пользователей: вход и](scenario-web-app-sign-user-sign-in.md)выход.

Выход отличается для веб-приложения, которое вызывает веб-API. Когда пользователь выходит из приложения или из любого приложения, необходимо удалить маркеры, связанные с этим пользователем, из кэша маркеров.

## <a name="intercept-the-callback-after-single-sign-out"></a>Перехват обратного вызова после единого выхода

Чтобы очистить запись кэша маркеров, связанную с учетной записью, в которой выполнен выход из службы, приложение может перехватить `logout` событие After. Веб-приложения хранят маркеры доступа для каждого пользователя в кэше маркеров. Перехватывая `logout` ответный вызов после обратного вызова, веб-приложение может удалить пользователя из кэша.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity. Web берет на себя реализацию выхода. Дополнительные сведения см. в разделе [Исходный код Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L168-L176) .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Пример ASP.NET не удаляет учетные записи из кэша при глобальном выходе.

# <a name="java"></a>[Java](#tab/java)

Пример Java не удаляет учетные записи из кэша при глобальном выходе.

# <a name="python"></a>[Python](#tab/python)

Пример Python не удаляет учетные записи из кэша при глобальном выходе.

---

## <a name="next-steps"></a>Дальнейшие действия

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Получение маркера для веб-приложения](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Получение маркера для веб-приложения](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Получение маркера для веб-приложения](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Получение маркера для веб-приложения](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
