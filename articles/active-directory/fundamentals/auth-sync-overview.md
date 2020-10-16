---
title: Общие сведения о протоколе проверки подлинности Azure Active Directory и синхронизации
description: Руководство по архитектуре при достижении этого шаблона проверки подлинности
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d881dc3fe3e3caa1058cf97834735910b0de1d9
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114498"
---
# <a name="azure-active-directory-integrations-with-legacy-authentication-and-synchronization-protocols"></a>Интеграция Azure Active Directory с устаревшими протоколами проверки подлинности и синхронизации

Microsoft Azure Active Directory (Azure AD) обеспечивает интеграцию с множеством протоколов проверки подлинности и синхронизации. Интеграция аутентификации позволяет использовать Azure AD, а также функции безопасности и управления с минимальными изменениями в приложениях, использующих устаревшие методы проверки подлинности. Интеграция синхронизации позволяет синхронизировать данные пользователей и групп с Azure AD, а затем выполнять функции управления пользователями Azure AD. Некоторые шаблоны синхронизации также обеспечивают автоматическую подготовку.

## <a name="authentication-patterns"></a>Модели проверки подлинности

В следующей таблице представлены шаблоны проверки подлинности и их возможности. Выберите имя шаблона проверки подлинности для просмотра

* Подробное описание

* Сценарии использования

* Схема архитектуры

* Объяснение компонентов системы

* Ссылки для реализации интеграции

 

| Модели проверки подлинности| Аутентификация| Авторизация| Многофакторная идентификация| Условный доступ |
| - |- | - | - | - |
| [Проверка подлинности на основе заголовков](auth-header-based.md)|![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png) |
| [Проверка подлинности LDAP](auth-ldap.md)| ![флажок](./media/authentication-patterns/check.png)| | |  |
| [Аутентификация OAuth 2,0](auth-oauth2.md)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png) |
| [Проверка подлинности OIDC](auth-oidc.md)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png) |
| [Проверка подлинности единого входа на основе пароля](auth-password-based-sso.md )| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png) |
| [Проверка подлинности RADIUS]( auth-radius.md)| ![флажок](./media/authentication-patterns/check.png)| | ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png) |
| [Службы шлюза удаленный рабочий стол](auth-remote-desktop-gateway.md)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png) |
| [Проверка подлинности SAML](auth-saml.md).| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png) |
| [Проверка подлинности Windows — ограниченное делегирование Kerberos](auth-kcd.md)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>Шаблоны синхронизации

В следующей таблице представлены шаблоны синхронизации и их возможности. Выберите имя шаблона для просмотра

* Подробное описание

* Сценарии использования

* Схема архитектуры

* Объяснение компонентов системы

* Ссылки для реализации интеграции



| Шаблон синхронизации| Синхронизация каталогов| Подготовка пользователей |
| - | - | - |
| [Синхронизация каталогов](sync-directory.md)| ![флажок](./media/authentication-patterns/check.png)|  |
| [Синхронизация LDAP](sync-ldap.md)| ![флажок](./media/authentication-patterns/check.png)|  |
| [Синхронизация SCIM](sync-scim.md)| ![флажок](./media/authentication-patterns/check.png)| ![флажок](./media/authentication-patterns/check.png) |

