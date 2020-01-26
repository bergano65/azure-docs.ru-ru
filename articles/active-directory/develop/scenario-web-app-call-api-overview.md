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
ms.openlocfilehash: d121d6c198cb0d92cd098a40096e2f2300f65537
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758995"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Сценарий: веб-приложение, вызывающее веб-API

Узнайте, как создать веб-приложение, которое подписывает пользователей на платформу Microsoft Identity, а затем вызывает веб-API от имени пользователя, выполнившего вход.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

В этом сценарии предполагается, что вы уже выполнили следующие сценарии:

> [!div class="nextstepaction"]
> [Веб-приложение, которое поддерживает вход пользователей](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Обзор

Вы можете добавить проверку подлинности в веб-приложение, чтобы он мог подписывать пользователей и вызывать веб-API от имени пользователя, выполнившего вход.

![Веб-приложение, которое вызывает веб-API](./media/scenario-webapp/web-app.svg)

Веб-приложения, вызывающие веб-API, являются конфиденциальными клиентскими приложениями.
Вот почему они регистрируют секрет (пароль приложения или сертификат) с помощью Azure Active Directory (Azure AD). Этот секрет передается во время вызова Azure AD для получения маркера.

## <a name="specifics"></a>Особенности

> [!NOTE]
> Добавление входа в веб-приложение — это защита самого веб-приложения. Эта защита достигается с помощью библиотек по *промежуточного слоя* , а не библиотеки проверки подлинности Майкрософт (MSAL). Приведенный выше сценарий — [веб-приложение, которое выполняет вход пользователей](scenario-web-app-sign-user-overview.md).
>
> В этом сценарии описывается вызов веб-API из веб-приложения. Для этих веб-API необходимо получить маркеры доступа. Чтобы получить эти маркеры, используйте библиотеки MSAL для получения этих маркеров.

Разработка для этого сценария включает следующие конкретные задачи:

- Во время [регистрации приложения](scenario-web-app-call-api-app-registration.md)необходимо предоставить универсальный код ресурса (URI) ответа, секрет или сертификат для совместного использования с Azure AD. При развертывании приложения в нескольких расположениях эти сведения будут предоставлены для каждого расположения.
- [Конфигурация приложения](scenario-web-app-call-api-app-configuration.md) должна предоставлять учетные данные клиента, которые были предоставлены для общего доступа к Azure AD во время регистрации приложения.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Веб-приложение, вызывающее веб-API: регистрация приложения](scenario-web-app-call-api-app-registration.md)
