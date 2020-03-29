---
title: Поддержка Azure AD FS (MSAL Python)
titleSuffix: Microsoft identity platform
description: Узнайте о поддержке Службы Федерации активных каталогов (AD FS) в Библиотеке аутентификации Майкрософт для Python
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01d4cb626aabc83117e864b75b49eec63a6c0af0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699552"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Поддержка активных служб Федерации каталогов в MSAL для Python

Службы Федерации активных директоров (AD FS) в Windows Server позволяют добавлять OpenID Connect и OAuth 2.0 на основе аутентификации и авторизации в приложения с помощью библиотеки подлинности Microsoft (MSAL) для Python. Используя MSAL для библиотеки Python, приложение может проверить подлинность пользователей непосредственно против AD FS. Для получения дополнительной информации о сценариях [см.](/windows-server/identity/ad-fs/ad-fs-development)

Есть, как правило, два способа аутентификации против AD FS:

- MSAL Python ведет переговоры с Azure Active Directory, который сам по себе является federated с другими поставщиками идентификационных данных. Федерация происходит через AD FS. MSAL Python подключается к Azure AD, который подписывается в пользователях, управляемых в Azure AD (управляемых пользователей) или пользователях, управляемых другим поставщиком идентификационных данных, таких как AD FS (федеративные пользователи). MSAL Python не знает, что пользователь является федеративным. Он просто разговаривает с Azure AD. [Орган,](msal-client-application-configuration.md#authority) который вы используете в этом случае, является обычным органом (имя хозяина власти , арендатор, общий или организации).
- MSAL Python беседует непосредственно с органом AD FS. Это поддерживается только AD FS 2019 и позже.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Подключение к Active Directory федеративный с AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Приобретите токен в интерактивном режиме для федеративного пользователя

Ниже приводится вопрос о том, подключаетесь ли вы непосредственно к службам Active Directory Federation Services (AD FS) или через Active Directory.

При `acquire_token_by_authorization_code` вызове `acquire_token_by_device_flow`или, пользовательский опыт, как правило, следующим образом:

1. Пользователь вводит идентификатор учетной записи.
2. AD Azure отображает краткосообщение «Забрасывая вас на страницу организации», и пользователь перенаправляется на страницу поставщика идентификационных данных. Страница ввне обычно настраивается с логотипом организации.

Поддерживаемые версии AD FS в этом федеративном сценарии:
- Активные услуги Федерации каталога FS v2
- Активные службы Федерации каталогов v3 (Windows Server 2012 R2)
- Активные услуги Федерации каталога v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Приобретите токен с помощью имени пользователя и пароля

Ниже приводится вопрос о том, подключаетесь ли вы непосредственно к службам Active Directory Federation Services (AD FS) или через Active Directory.

При приобретении токена с помощью `acquire_token_by_username_password`MSAL Python получает контакт с поставщиком идентификационных данных на основе имени пользователя. MSAL Python получает [токен SAML 1.1](reference-saml-tokens.md) от поставщика идентификационных данных, который он затем предоставляет Azure AD, который возвращает токен JSON Web (JWT).

## <a name="connecting-directly-to-ad-fs"></a>Подключение непосредственно к AD FS

При подключении каталога к AD FS, орган, который вы хотите использовать для создания приложения будет что-то вроде`https://somesite.contoso.com/adfs/`

MSAL Python поддерживает ADFS 2019.

Он не поддерживает прямое подключение к ADFS 2016 или ADFS v2. Если вам необходимо поддерживать сценарии, требующие прямого подключения к ADFS 2016, используйте последнюю версию ADAL Python. После обновления предприимшнивой системы до ADFS 2019, вы можете использовать MSAL Python.

## <a name="next-steps"></a>Дальнейшие действия

- В случае с федеративной стороной см. [Например, в поведении приложения можно найти знак «Настройка активного каталога» с помощью политики Home Realm Discovery](../manage-apps/configure-authentication-for-federated-users-portal.md)