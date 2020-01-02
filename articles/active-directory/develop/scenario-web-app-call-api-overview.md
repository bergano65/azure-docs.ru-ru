---
title: Создание веб-приложения, вызывающего веб-API — платформа Microsoft Identity | Службы
description: Узнайте, как создать веб-приложение, вызывающее веб-API (обзор)
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
ms.openlocfilehash: 3888c7f838d6009382f849bc7d3e34c49b3b70a4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962140"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Сценарий: веб-приложение, вызывающее веб-API

Узнайте, как создать веб-приложение, которое будет входить в систему пользователей на платформе Microsoft Identity, а затем вызвать веб-API от имени пользователя, выполнившего вход.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

В этом сценарии предполагается, что вы выполнили следующий сценарий:

> [!div class="nextstepaction"]
> [Веб-приложение, которое входит в систему пользователей](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Краткое описание

Вы добавляете проверку подлинности в веб-приложение, которое, следовательно, может выполнять вход пользователей и вызывать веб-API от имени пользователя, выполнившего вход.

![Веб-приложение, которое вызывает веб-API](./media/scenario-webapp/web-app.svg)

Веб-приложения, вызывающие веб-API:

- — Это конфиденциальные клиентские приложения.
- Вот почему они зарегистрировали секрет (пароль приложения или сертификат) в Azure AD. Этот секрет передается во время вызова Azure AD для получения маркера

## <a name="specifics"></a>Особенности

> [!NOTE]
> Добавление входа в веб-приложение не использует библиотеки MSAL, так как это обеспечивает защиту веб-приложения. Защита библиотек достигается с помощью библиотек по промежуточного слоя. Это был объект предыдущего сценария [входа пользователей в веб-приложение](scenario-web-app-sign-user-overview.md) .
>
> При вызове веб-API из веб-приложения вам потребуется получить маркеры доступа для этих веб-API. Для получения этих маркеров можно использовать библиотеки MSAL.

У разработчиков, которые в итоге приводят к завершению работы в этом сценарии, есть следующие особенности.

- Во время [регистрации приложения](scenario-web-app-call-api-app-registration.md)необходимо указать одно или несколько (если вы развертываете приложение в нескольких расположениях) URI ответа, секреты или сертификаты, которые должны быть совместно использоваться с Azure AD.
- При регистрации приложения в [конфигурации приложения](scenario-web-app-call-api-app-configuration.md) необходимо предоставить учетные данные клиента совместно с Azure AD.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](scenario-web-app-call-api-app-registration.md)
