---
title: Вход пользователей из веб-приложения — платформа Microsoft Identity | Службы
description: Узнайте, как создать веб-приложение, которое входит в систему пользователей (обзор)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a53f88a97cdba8626c9d6c421093e4ec327e19
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74961613"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Сценарий: веб-приложение, которое входит в систему пользователей

Узнайте все, что нужно для создания веб-приложения, использующего платформу Microsoft Identity для входа пользователей.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Начало работы

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Если вы хотите создать первое переносимое веб-приложение (ASP.NET Core), которое входит в систему пользователей, следуйте указаниям в этом кратком руководстве:

> [!div class="nextstepaction"]
> [Краткое руководство. ASP.NET Core веб-приложение, которое входит в систему пользователей](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Если вы хотите разобраться, как добавить вход в существующее веб-приложение ASP.NET, воспользуйтесь приведенным ниже кратким руководством.

> [!div class="nextstepaction"]
> [Краткое руководство. веб-приложение ASP.NET, которое входит в систему пользователей](quickstart-v2-aspnet-webapp.md)

# <a name="javatabjava"></a>[Java](#tab/java)

Если вы являетесь разработчиком Java, воспользуйтесь приведенным ниже кратким руководством.

> [!div class="nextstepaction"]
> [Краткое руководство. Добавление входа с помощью Microsoft в веб-приложение Java](quickstart-v2-java-webapp.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

Если вы разрабатываете с помощью Python, попробуйте выполнить следующие действия в этом кратком руководстве:

> [!div class="nextstepaction"]
> [Краткое руководство. Добавление входа с помощью Microsoft в веб-приложение Python](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Краткое описание

Вы можете добавить проверку подлинности в веб-приложение, чтобы он мог выполнять вход пользователей. Добавление проверки подлинности позволяет веб-приложению получить доступ к сведениям о профиле, чтобы настроить взаимодействие с пользователями. 

Веб-приложения проверяют подлинность пользователя в веб-браузере. В этом сценарии веб-приложение направляет браузер пользователя на вход в Azure Active Directory (Azure AD). Azure AD возвращает ответ на вход через браузер пользователя, который содержит утверждения о пользователе в маркере безопасности. Вход пользователей использует преимущества стандартного протокола [Open ID Connect](./v2-protocols-oidc.md) , упрощенного с помощью [библиотек](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)по промежуточного слоя.

![Выполнение входа пользователей в веб-приложении](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

На втором этапе можно разрешить приложению вызывать веб-API от имени пользователя, выполнившего вход. Следующий этап — другой сценарий, который можно найти в [веб-приложении, которое вызывает веб-API](scenario-web-app-call-api-overview.md).

> [!NOTE]
> Добавление входа в веб-приложение — это защита веб-приложения и проверка маркера пользователя, который выполняется библиотеками по **промежуточного слоя** . В случае с .NET этот сценарий еще не требует использования библиотеки проверки подлинности Майкрософт (MSAL), что заключается в получении маркера для вызова защищенных API. Библиотеки проверки подлинности будут представлены в сценарии дальнейших действий, когда веб-приложению необходимо вызвать веб-API.

## <a name="specifics"></a>Особенности

- Во время регистрации приложения необходимо предоставить один или несколько (при развертывании приложения в нескольких расположениях) URI ответа. В некоторых случаях (ASP.NET и ASP.NET Core) необходимо включить маркер идентификации. Наконец, необходимо настроить универсальный код ресурса (URI) выхода, чтобы приложение передействовало на выход пользователей.
- В коде для вашего приложения необходимо предоставить центр, в котором веб-приложение делегирует вход. Может потребоваться настроить проверку маркера (в частности, в сценариях партнеров).
- Веб-приложения поддерживают любые типы учетных записей. Дополнительные сведения см. в разделе [Поддерживаемые типы учетных записей](v2-supported-account-types.md).

## <a name="next-steps"></a>Дальнейшие действия

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
