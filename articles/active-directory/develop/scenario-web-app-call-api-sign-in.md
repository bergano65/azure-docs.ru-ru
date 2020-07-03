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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e138b3513b42dda47b0a114d866d657e18e3e393
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181653"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Веб-приложение, вызывающее веб-API: удаление учетных записей из кэша маркеров при глобальном выходе

Вы узнали, как добавить вход в веб-приложение в [веб-приложении, которое входит в систему пользователей: вход и](scenario-web-app-sign-user-sign-in.md)выход.

Выход отличается для веб-приложения, которое вызывает веб-API. Когда пользователь выходит из приложения или из любого приложения, необходимо удалить маркеры, связанные с этим пользователем, из кэша маркеров.

## <a name="intercept-the-callback-after-single-sign-out"></a>Перехват обратного вызова после единого выхода

Чтобы очистить запись кэша маркеров, связанную с учетной записью, в которой выполнен выход из службы, `logout` приложение может перехватить событие After. Веб-приложения хранят маркеры доступа для каждого пользователя в кэше маркеров. Перехватывая ответный вызов `logout` после обратного вызова, веб-приложение может удалить пользователя из кэша.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity. Web берет на себя реализацию выхода.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Пример ASP.NET не удаляет учетные записи из кэша при глобальном выходе.

# <a name="java"></a>[Java](#tab/java)

Пример Java не удаляет учетные записи из кэша при глобальном выходе.

# <a name="python"></a>[Python](#tab/python)

Пример Python не удаляет учетные записи из кэша при глобальном выходе.

---

## <a name="next-steps"></a>Следующие шаги

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
