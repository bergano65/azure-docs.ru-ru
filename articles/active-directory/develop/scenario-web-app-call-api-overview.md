---
title: Создайте веб-приложение, которое вызывает веб-AI - идентификационная платформа Microsoft Azure
description: Узнайте, как создать веб-приложение, которое вызывает веб-AIS (обзор)
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758995"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Сценарий: Веб-приложение, которое вызывает веб-AIS

Узнайте, как создать веб-приложение, которое подписывает пользователей на платформу идентификации Майкрософт, а затем вызывает веб-AA от имени пользователя, вписанного в нее.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Этот сценарий предполагает, что вы уже прошли через следующий сценарий:

> [!div class="nextstepaction"]
> [Веб-приложение, которое подписывает в пользователях](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Обзор

Вы добавляете аутентификацию в свое веб-приложение, чтобы оно мог войти в систему и вызвать веб-API от имени пользователя, вписавого в систему.

![Веб-приложение, которое вызывает веб-API](./media/scenario-webapp/web-app.svg)

Веб-приложения, которые называют веб-AI, являются конфиденциальными клиентскими приложениями.
Именно поэтому они регистрируют секретный (пароль приложения или сертификат) в Azure Active Directory (Azure AD). Этот секрет передается во время вызова в Azure AD, чтобы получить токен.

## <a name="specifics"></a>Специфики

> [!NOTE]
> Добавление вху-ин в веб-приложение о защите самого веб-приложения. Эта защита достигается с помощью библиотек *промежуточного посуды,* а не библиотеки подлинности Майкрософт (MSAL). Предыдущий сценарий, [веб-приложение, которое подписывает в пользователях](scenario-web-app-sign-user-overview.md), охватывает эту тему.
>
> Этот сценарий охватывает, как вызвать веб-aIS из веб-приложения. Вы должны получить токены доступа для этих веб-AIS. Чтобы приобрести эти токены, вы используете библиотеки MSAL для приобретения этих токенов.

Разработка для этого сценария включает в себя следующие конкретные задачи:

- Во время [регистрации заявления](scenario-web-app-call-api-app-registration.md)вы должны предоставить ответ URI, секрет или сертификат, который будет передан Azure AD. Если вы развернете приложение в нескольких местах, вы предоставите эту информацию для каждого местоположения.
- [Конфигурация приложения](scenario-web-app-call-api-app-configuration.md) должна предоставить учетные данные клиента, которые были переданы Azure AD во время регистрации приложения.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Веб-приложение, которое вызывает веб-AIS: Регистрация приложений](scenario-web-app-call-api-app-registration.md)
