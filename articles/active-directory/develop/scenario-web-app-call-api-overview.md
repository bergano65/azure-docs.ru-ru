---
title: Веб-приложения, что вызовы веб-API-интерфейсы (Обзор) - платформой Microsoft identity
description: Узнайте, как создать веб-приложение, вызовы веб-API-интерфейсы (Обзор)
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
ms.openlocfilehash: ce45f11a697b72ebdd0fe01166a70e7b47aa8e9f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076305"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Сценарий. Веб-приложения, что вызовы веб-API

Сведения о создании пользователей при входе в систему веб-приложения на платформе Microsoft identity и вызовы веб-API от имени выполнившего вход пользователя.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Этот сценарий supposes, что вы прошли следующий сценарий:

> [!div class="nextstepaction"]
> [Веб-приложение, где пользователи, впервые входящий в систему](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Обзор

Добавление проверки подлинности в веб-приложения, который могут входить пользователи, а также вызывает веб-API от имени выполнившего вход пользователя.

![Веб-приложения, что вызовы веб-API](./media/scenario-webapp/web-app.svg)

Веб-приложений, которые вызывает веб-API:

- — Это конфиденциального клиента приложения.
- Вот почему они зарегистрировали секрет (пароль приложения или сертификат) с Azure AD. Этот секрет переданное при вызове Azure AD для получения токена.

## <a name="specifics"></a>Особенности

> [!NOTE]
> Добавление входа в веб-приложение не использует библиотеки MSAL, так как это о защите веб-приложения. Защита библиотеки достигается за счет библиотеки с именем по промежуточного слоя. Это был объект в предыдущем сценарии [вход пользователей в веб-приложение](scenario-web-app-sign-user-overview.md)
>
> При вызове веб-API из веб-приложения, необходимо будет получить маркеры доступа для этих веб-API. Библиотеки MSAL можно использовать для получения этих маркеров.

Работы разработчиков в этом сценарии есть, таким образом, определенных аспектов, как:

- Во время [Регистрация приложения](scenario-web-app-call-api-app-registration.md), вам потребуется указать, или несколько (Если развертывание приложения в нескольких местах) ответить URI, секретные данные, либо сертификаты должны использоваться совместно с Azure AD.
- [Конфигурации приложения](scenario-web-app-call-api-app-configuration.md) должен предоставить учетные данные клиента, общего доступа с помощью Azure AD во время регистрации приложения

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](scenario-web-app-call-api-app-registration.md)
