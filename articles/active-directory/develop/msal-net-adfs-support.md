---
title: Поддержка AD FS в MSAL.NET Azure
titleSuffix: Microsoft identity platform
description: Узнайте об поддержке Active Directory Federation Services (AD FS) в Библиотеке аутентификации Майкрософт для .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 15af18177cea217612a4d5276d130abe02d339f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160766"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Поддержка активных служб Федерации каталогов в MSAL.NET
Службы Федерации активных директоров (AD FS) в Windows Server позволяют добавлять OpenID Connect и OAuth 2.0 на основе аутентификации и авторизации в разрабатываемые приложения. Эти приложения могут, таким образом, аутентифицировать пользователей непосредственно против AD FS. Для получения дополнительной информации прочитайте [сценарии AD FS для разработчиков](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios).

Библиотека аутентификации Майкрософт для .NET (MSAL.NET) поддерживает два сценария проверки подлинности в отношении AD FS:

- MSAL.NET беседует с Azure Active Directory, который сам по себе *используется* AD FS.
- MSAL.NET беседует **непосредственно** с органом ADFS. Это поддерживается только от AD FS 2019 и выше. Одним из сценариев, окоторых ирисуемом является поддержка [Azure Stack](https://azure.microsoft.com/overview/azure-stack/)


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL подключается к Azure AD, который федеративно с aD FS
MSAL.NET поддерживает подключение к Azure AD, который подписывает управляемых пользователей (пользователей, управляемых в Azure AD) или федеративных пользователей (пользователей, управляемых другим поставщиком идентификационных данных, таких как AD FS). MSAL.NET не знает о том, что пользователи являются федеративными. Что касается его, то он разговаривает с Azure AD.

[Орган,](msal-client-application-configuration.md#authority) который вы используете в этом случае, является обычным органом (имя хозяина власти , арендатор, общий или организации).

### <a name="acquiring-a-token-interactively"></a>Приобретение токена в интерактивном режиме
При вызове `AcquireTokenInteractive` метода пользовательский интерфейс обычно:

1. Пользователь вводит идентификатор учетной записи.
2. Azure AD отображает краткосообщение «Выведет вас на страницу организации».
3. Пользователь перенаправляется на страницу регистрации поставщика идентификационных данных. Страница ввне обычно настраивается с логотипом организации.

Поддерживаемые версии AD FS в этом федеративном сценарии: AD FS v2, AD FS v3 (Windows Server 2012 R2) и AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Приобретение токена с помощью AcquireTokenByIntegratedAuthentication или AcquireTokenByUsernamePassword
При приобретении токена с помощью `AcquireTokenByIntegratedAuthentication` или `AcquireTokenByUsernamePassword` методов MSAL.NET получает, чтобы поставщик удостоверяющих данных связался на основе имени пользователя.  MSAL.NET получает [токен SAML 1.1](reference-saml-tokens.md) после обращения к поставщику удостоверений личности.  MSAL.NET затем предоставляет токен SAML Azure AD в качестве утверждения пользователя (по аналогии с [потоком от имени),](msal-authentication-flows.md#on-behalf-of)чтобы получить обратно JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL подключается непосредственно к AD FS
MSAL.NET поддерживает подключение к AD FS 2019, который соответствует требованиям Open ID Connect и понимает PKCE и области. Эта поддержка требует, чтобы пакет услуг [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) был применен к Windows Server. При подключении непосредственно к AD FS, полномочия, которые вы хотите `https://mysite.contoso.com/adfs/`использовать для создания приложения, аналогичны .

В настоящее время нет планов по поддержке прямой связи с:

- AD FS 16, так как он не поддерживает PKCE и по-прежнему использует ресурсы, а не область
- AD FS v2, который не соответствует требованиям OIDC.

 Если вам нужна поддержка сценариев, требующих прямого подключения к AD FS 2016, используйте последнюю версию [Библиотеки подлинности active Directory Library Azure.](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries) Когда вы модернизируете свою систему до AD FS 2019, вы сможете использовать MSAL.NET.

## <a name="next-steps"></a>Дальнейшие действия

В случае с федеративной стороной см. [Например, в поведении приложения можно найти знак «Настройка активного каталога» с помощью политики Home Realm Discovery](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)
