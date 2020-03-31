---
title: Регистрация daemon приложений, которые называют веб-AIS - Microsoft идентификационная платформа (ru) Azure
description: Узнайте, как создать приложение daemon, которое вызывает веб-AIS - регистрация приложений
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 922a484d111746e5073c08a64d7c92e2b6b4a7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773393"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Приложение Daemon, которое вызывает веб-AIS - регистрация приложений

Для приложения daemon, вот что вам нужно знать, когда вы регистрируете приложение.

## <a name="supported-account-types"></a>Поддерживаемые типы учетных записей

Приложения Daemon имеют смысл только у арендаторов Azure AD. Поэтому при создании приложения необходимо выбрать один из следующих вариантов:

- **Учетные записи только в этом организационном каталоге**. Этот выбор является наиболее распространенным, потому что приложения daemon обычно пишутся разработчиками line-of-business (LOB).
- **Счета в любом организационном каталоге**. Вы сделаете этот выбор, если вы ISV предоставления утилиты инструмент для ваших клиентов. Вам понадобятся клиентские админы, чтобы утвердить его.

## <a name="authentication---no-reply-uri-needed"></a>Аутентификация - ответ URI не требуется

В случае, если конфиденциальное клиентское приложение использует *только* поток учетных данных клиента, ответ URI не должен быть зарегистрирован. Это не нужно для конфигурации приложения или конструкции. Поток учетных данных клиента не использует его.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Разрешения API - разрешения на приложение и согласие админа

Приложение daemon может запрашивать только разрешения на применение к AA (не делегированные разрешения). На странице **разрешений API** для регистрации приложения после того, как вы выбрали **добавление разрешения** и выбрали семейство API, выберите **разрешения приложения,** а затем выберите разрешения.

![Разрешения на приложение и согласие админа](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> Веб-API, который вы хотите вызвать, должен определять *разрешения приложений (роли приложений),* а не делегировать разрешения. Для получения [подробной](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)информации о том, как разоблачить такой API, см.

Приложения Daemon требуют, чтобы админ-админ-арендатор дал согласие на вызов web-API. Админы-наниматели предоставляют это согласие на той же странице **разрешения API,** выбрав **согласие админа Гранта на *нашу организацию* **

Если вы ISV строите мультитенантное приложение, вы должны прочитать раздел [Развертывание - случай мультитенантных приложений daemon.](scenario-daemon-production.md#deployment---multitenant-daemon-apps)

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Приложение Daemon - конфигурация кода приложения](./scenario-daemon-app-configuration.md)
