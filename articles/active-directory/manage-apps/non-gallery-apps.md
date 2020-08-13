---
title: Использование Azure AD для приложений, не перечисленных в коллекции приложений
description: Узнайте, как интегрировать приложения, не перечисленные в коллекции Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/27/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: d4f00061664340681b365eddfa1a742795f96d99
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88164688"
---
# <a name="using-azure-ad-for-applications-not-listed-in-the-app-gallery"></a>Использование Azure AD для приложений, не перечисленных в коллекции приложений

В кратком руководстве [Добавление приложения](add-application-portal.md) вы узнаете, как добавить приложение в клиент Azure AD.

Кроме вариантов, доступных в [коллекции приложений Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/), можно добавить **приложение, которого нет в коллекции**. 

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Возможности для приложений, не перечисленных в коллекции Azure AD

Вы можете добавить любое приложение, которое уже есть в вашей организации, или любое приложение стороннего поставщика, которое не входит в коллекцию приложений Azure AD. В зависимости от [лицензионного соглашения](https://azure.microsoft.com/pricing/details/active-directory/) доступны следующие возможности:

- самостоятельная интеграция любого приложения, которое совместимо с поставщиками удостоверений [SAML (язык разметки заявлений системы безопасности) 2.0](https://wikipedia.org/wiki/SAML_2.0) (инициированная поставщиком услуг или поставщиком удостоверений);
- самостоятельная интеграция любого веб-приложения, где есть HTML-страница входа, с использованием функции [единого входа на основе пароля](sso-options.md#password-based-sso)
- самостоятельное подключение приложений, которые используют протокол [SCIM (System for Cross-domain Identity Management) для подготовки пользователей](../app-provisioning/use-scim-to-provision-users-and-groups.md);
- Возможность добавлять ссылки на любые приложения в [Средство запуска приложений Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) или [Панель доступа Azure AD](sso-options.md#linked-sign-on).

Если вы ищете руководство для разработчиков о том, как интегрировать пользовательские приложения с Azure AD, см. статью [сценарии проверки подлинности в Azure AD](../develop/authentication-scenarios.md). При разработке приложения, использующего современный протокол, например [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md), для аутентификации пользователей, вы можете зарегистрировать его на платформе удостоверений Майкрософт, используя функцию [Регистрация приложений](../develop/quickstart-register-app.md) на портале Azure.

## <a name="next-steps"></a>Дальнейшие действия

- [Циклическое руководство по управлению приложениями](view-applications-portal.md)

