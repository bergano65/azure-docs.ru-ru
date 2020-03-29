---
title: Войти в число пользователей из веб-приложения - платформа идентификации Майкрософт (ru) Azure
description: Узнайте, как создать веб-приложение, которое подписывается в пользователях (обзор)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 403f589702fd7142f0515a3b6f19ee1b9bbb6420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701558"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Сценарий: Веб-приложение, которое подписывает в пользователях

Узнайте все, что вам нужно для создания веб-приложения, которое использует платформу идентификации Майкрософт для регистрации пользователей.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Начало работы

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Если вы хотите создать свой первый портативный (ASP.NET Core) веб-приложение, которое подписывает в пользователей, следуйте этому quickstart:

> [!div class="nextstepaction"]
> [Быстрый запуск: ASP.NET основное веб-приложение, которое подписывает в пользователей](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Если вы хотите понять, как добавить в систему входном ASP.NET веб-приложении, попробуйте следующий быстрый запуск:

> [!div class="nextstepaction"]
> [Быстрый запуск: ASP.NET веб-приложение, которое подписывает в пользователей](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Если вы являетесь java-разработчиком, попробуйте следующий быстрый запуск:

> [!div class="nextstepaction"]
> [Быстрый запуск: Добавить входить в систему Microsoft в веб-приложение Java](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Если вы развиваетесь с Python, попробуйте следующий quickstart:

> [!div class="nextstepaction"]
> [Быстрый запуск: Добавить входить в систему Microsoft в веб-приложение Python](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Обзор

Вы добавляете аутентификацию в ваше веб-приложение, чтобы оно мог войти в число пользователей. Добавление аутентификации позволяет вашему веб-приложению получить доступ к информации ограниченного профиля, чтобы настроить интерфейс для пользователей. 

Веб-приложения аутентифицировать пользователя в веб-браузере. В этом случае веб-приложение направляет браузер пользователя для вхотливки в Active Directory Azure (Azure AD). Azure AD возвращает ответ на регистрацию через браузер пользователя, содержащий утверждения о пользователе в маркере безопасности. Подписание в пользователях использует стандартный протокол [Open ID Connect,](./v2-protocols-oidc.md) упрощенный с помощью [библиотек](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)промежуточного посуды.

![Выполнение входа пользователей в веб-приложении](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

В качестве второго этапа можно включить приложение для вызова web-aI от имени пользователя, вписавого в нее. Следующий этап — это другой сценарий, который вы найдете в [веб-приложении, которое вызывает web-апотили.](scenario-web-app-call-api-overview.md)

> [!NOTE]
> Добавление вхинга в веб-приложение — это защита веб-приложения и проверка токена пользователя, что и делают библиотеки **посредника.** В случае .NET этот сценарий пока не требует библиотеки подлинности Майкрософт (MSAL), которая занимается приобретением токена для вызова защищенных AIS. Библиотеки аутентификации будут введены в сценарии последующего наблюдения, когда веб-приложению необходимо вызывать web-аДИ.

## <a name="specifics"></a>Специфики

- Во время регистрации приложения необходимо предоставить один или несколько (если вы развернете приложение в нескольких местах) ответить на URIs. В некоторых случаях (ASP.NET и ASP.NET Core) необходимо включить маркер ID. Наконец, вы хотите настроить вывеску URI, чтобы ваше приложение реагировала на подписку о пользователях.
- В коде приложения необходимо предоставить полномочия, на которые введем в систему делегаты веб-приложения. Возможно, необходимо настроить проверку токенов (в частности, в сценариях партнеров).
- Веб-приложения поддерживают любые типы учетных записей. Для получения дополнительной информации [см.](v2-supported-account-types.md)

## <a name="next-steps"></a>Дальнейшие действия

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Регистрация приложения](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Регистрация приложения](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Регистрация приложения](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Регистрация приложения](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
