---
title: Поддержка AD FS в библиотеке проверки подлинности Microsoft .NET | Azure
description: Сведения о поддержке служб федерации Active Directory (AD FS) в библиотеке проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/03/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5963c5e83b5af3848edd934328caa1f095bd184
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676733"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Поддержка служб федерации Active Directory в MSAL.NET
Федерации служб Active Directory (AD FS) в Windows Server позволяет добавлять OpenID Connect и проверки подлинности на основе OAuth 2.0 и авторизации для приложений, вы разрабатываете и эти приложения проверки подлинности пользователей непосредственно в AD FS. Дополнительные сведения в статье [сценарии AD FS для разработчиков](/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers).

Библиотека проверки подлинности Майкрософт для .NET (MSAL.NET) поддерживает два сценария для проверки подлинности в AD FS.

- MSAL.NET взаимодействует с Azure Active Directory, который сам является *federated* с AD FS.
- MSAL.NET talks *непосредственно* в службу AD FS, где версии AD FS — свести к (начиная с AD FS 2019 г.). Прямое подключение к AD FS позволяет MSAL.NET для проверки подлинности для приложений, выполняющихся в [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).

## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL подключается к Azure AD, который входит в федерацию с AD FS
MSAL.NET поддерживает подключение к Azure AD, который входит в управляемый пользователей (управляемых в Azure AD) или федеративных пользователей (под управлением другой поставщик удостоверений, например AD FS). MSAL.NET не знает о том, что пользователи входят в федерацию. Так как он занимается, он обращается к Azure AD.

[Центра](msal-client-application-configuration.md#authority) можно использовать в данном случае является обычным полномочия (имя узла центра + клиента, общие или организации).

### <a name="acquiring-a-token-interactively"></a>Получение токена в интерактивном режиме
При вызове `AcquireTokenInteractive` , взаимодействие с пользователем является обычно:

1. Пользователь вводит идентификатор своей учетной записи.
2. Azure AD, кратко, отображается сообщение «Выполняется переход на страницу вашей организации».
3. Пользователь перенаправляется на страницу входа поставщика удостоверений. Страница входа обычно настраивается с логотипом организации.

Поддерживаемые версии AD FS в этот интегрированный сценарий: v2 AD FS, AD FS v3 (Windows Server 2012 R2) и v4 службы федерации Active Directory (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Получение маркера с использованием AcquireTokenByIntegratedAuthentication или AcquireTokenByUsernamePassword
При получении маркер с помощью `AcquireTokenByIntegratedAuthentication` или `AcquireTokenByUsernamePassword` методы, MSAL.NET возвращает поставщик удостоверений для связи на основании имени пользователя.  Получает MSAL.NET [токен SAML 1.1](reference-saml-tokens.md) после обращения к поставщику удостоверений.  MSAL.NET затем предоставляет маркер SAML в Azure AD как утверждение пользователя (аналогичную [поток on-behalf-of](msal-authentication-flows.md#on-behalf-of)) для возврата маркера JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL подключается непосредственно к AD FS
MSAL.NET поддерживает подключение к AD FS 2019 г., — Open ID Connect требованиям. При подключении непосредственно к AD FS, полномочия, необходимо использовать для построения приложения аналогична `https://mysite.contoso.com/adfs/`.

В настоящее время не планируем поддерживать прямое подключение к AD FS 2016 или службы федерации Active Directory версии 2 (которые не свести к). Если необходима поддержка сценариев, требующих прямое подключение к AD FS 2016, используйте последнюю версию [библиотеку аутентификации Azure Active Directory](active-directory-authentication-libraries.md#microsoft-supported-client-libraries). В AD FS 2019 обновления вашей локальной системе, вы сможете использовать MSAL.NET.
