---
title: Поддержка AD FS Azure в библиотеке проверки подлинности Майкрософт для Python
titleSuffix: Microsoft identity platform
description: Дополнительные сведения о поддержке службы федерации Active Directory (AD FS) (AD FS) в библиотеке проверки подлинности Майкрософт для Python
services: active-directory
documentationcenter: dev-center-name
author: abhidnya13
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 148b9a4a890b22db63f03c673f5d779547a589e1
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485033"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Поддержка службы федерации Active Directory (AD FS) в MSAL для Python

Службы федерации Active Directory (AD FS) (AD FS) в Windows Server позволяет добавить проверку подлинности и авторизацию на основе OpenID Connect Connect и OAuth 2,0 в приложения с помощью библиотеки проверки подлинности Майкрософт (MSAL) для Python. С помощью MSAL для библиотеки Python приложение может проверять подлинность пользователей непосредственно в AD FS. Дополнительные сведения о сценариях см. в разделе [сценарии AD FS для разработчиков](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers).

Существует два способа проверки подлинности в AD FS.

- MSAL Python обращается к Azure Active Directory, которая является Федеративной с другими поставщиками удостоверений. Федерация происходит через AD FS. MSAL Python подключается к Azure AD, который выполняет вход пользователей, управляемых в Azure AD (управляемых пользователей), или пользователей, управляемых другим поставщиком удостоверений, например AD FS (Федеративные пользователи). MSAL Python не знает, что пользователь является федеративным. Это просто говорит с Azure AD. В этом случае в качестве [центра](msal-client-application-configuration.md#authority) используется обычный центр (имя узла центра сертификации + клиент, общие или организации).
- MSAL Python напрямую обращается к центру AD FS. Поддерживается только AD FS 2019 и более поздних версий.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Подключение к Active Directory федеративным с AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Получение маркера в интерактивном режиме для федеративного пользователя

Приведенные ниже зависимости применяются непосредственно к службы федерации Active Directory (AD FS) (AD FS) или через Active Directory.

При вызове `acquire_token_by_authorization_code` или `acquire_token_by_device_flow`интерфейс пользователя обычно выглядит следующим образом:

1. Пользователь вводит идентификатор своей учетной записи.
2. В Azure AD отображается краткое сообщение "идет переход на страницу вашей организации", и пользователь перенаправляется на страницу входа поставщика удостоверений. Страница входа обычно настраивается с помощью логотипа Организации.

Поддерживаемые версии AD FS в этом Федеративной ситуации:
- службы федерации Active Directory (AD FS) FS v2
- Службы федерации Active Directory (AD FS) v3 (Windows Server 2012 R2)
- Службы федерации Active Directory (AD FS) v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Получение маркера с помощью имени пользователя и пароля

Приведенные ниже зависимости применяются непосредственно к службы федерации Active Directory (AD FS) (AD FS) или через Active Directory.

Когда вы получаете маркер с помощью `acquire_token_by_username_password`, MSAL Python Получает поставщик удостоверений для связи на основе имени пользователя. MSAL Python получает [токен SAML 1,1](reference-saml-tokens.md) от поставщика удостоверений, который затем предоставляет Azure AD, который возвращает JSON Web Token (JWT).

## <a name="connecting-directly-to-ad-fs"></a>Прямое подключение к AD FS

При подключении каталога к AD FS центр, который вы хотите использовать для создания приложения, будет выглядеть примерно так `https://somesite.contoso.com/adfs/`

MSAL Python поддерживает ADFS 2019.

Он не поддерживает прямое подключение к ADFS 2016 или ADFS v2. Если вам требуется поддержка сценариев, требующих прямого подключения к ADFS 2016, используйте последнюю версию ADAL Python. После обновления локальной системы до ADFS 2019 можно использовать MSAL Python.

## <a name="next-steps"></a>Дополнительная информация

- Сведения об объединенном варианте см. в статье [Настройка поведения при входе Azure Active Directory для приложения с помощью политики обнаружения домашней области](../manage-apps/configure-authentication-for-federated-users-portal.md) .