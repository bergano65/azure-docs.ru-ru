---
title: Поддержка AD FS (MSAL для Java)
titleSuffix: Microsoft identity platform
description: Узнайте об поддержке Active Directory Federation Services (AD FS) в Библиотеке аутентификации Майкрософт для Java (MSAL4j).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 3d834a8d1524595304c22fed9897094622dfd93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696220"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Поддержка активных служб Федерации каталогов в MSAL для Java

Службы Федерации активных директоров (AD FS) в Windows Server позволяют добавлять OpenID Connect и OAuth 2.0 на основе аутентификации и авторизации в вашу библиотеку подлинности Майкрософт для Java (MSAL for Java). После интеграции приложение может аутентифицировать пользователей в AD FS, федеративную через Azure AD. Для получения дополнительной информации о сценариях [см.](/windows-server/identity/ad-fs/ad-fs-development)

Приложение, использующее MSAL для Java, будет общаться с Azure Active Directory (Azure AD), который затем поддается AD FS.

MSAL для Java подключается к Azure AD, который подписывается в пользователях, управляемых в Azure AD (управляемых пользователей) или пользователях, управляемых другим поставщиком идентификационных данных, таких как AD FS (федеративные пользователи). MSAL для Java не знает, что пользователь является федеративным. Он просто разговаривает с Azure AD.

[Орган,](msal-client-application-configuration.md#authority) который вы используете в этом случае, является обычным органом (имя хозяина власти , арендатор, общий или организации).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Приобретите токен в интерактивном режиме для федеративного пользователя

Когда вы `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` звоните или с `AuthorizationCodeParameters` или, `DeviceCodeParameters`пользовательский опыт, как правило:

1. Пользователь вводит идентификатор учетной записи.
2. AD Azure кратко отображает "Забирая вас на страницу организации", и пользователь перенаправляется на страницу входной связи поставщика идентификационных данных. Страница ввне обычно настраивается с логотипом организации.

Поддерживаемые версии AD FS в этом федеративном сценарии:
- Активные услуги Федерации каталога FS v2
- Активные службы Федерации каталогов v3 (Windows Server 2012 R2)
- Активные услуги Федерации каталога v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Приобретите токен с помощью имени пользователя и пароля

При приобретении токена `ConfidentialClientApplication.AcquireToken()` с использованием `PublicClientApplication.AcquireToken()` или с `IntegratedWindowsAuthenticationParameters` или, `UsernamePasswordParameters`MSAL для Java получает идентификационный провайдер связаться на основе имени пользователя. MSAL для Java получает [токен SAML 1.1](reference-saml-tokens.md) от поставщика идентификационных данных, который затем предоставляет Azure AD, который возвращает токен JSON Web (JWT).

## <a name="next-steps"></a>Дальнейшие действия

В случае с федеративной стороной см. [Например, в поведении приложения можно найти знак «Настройка активного каталога» с помощью политики Home Realm Discovery](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)