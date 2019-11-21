---
title: Use Microsoft identity platform to sign in users using resource owner password credential (ROPC) grant | Azure
description: Поддержка внебраузерных потоков аутентификации с помощью предоставления пароля владельца ресурса.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4504a1ae60aaac790ca15c120433159c2ff78fa
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207769"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credentials"></a>Microsoft identity platform and the OAuth 2.0 Resource Owner Password Credentials

Microsoft identity platform supports the [OAuth 2.0 Resource Owner Password Credentials (ROPC) grant](https://tools.ietf.org/html/rfc6749#section-4.3), which allows an application to sign in the user by directly handling their password.  This article describes how to program directly against the protocol in your application.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](sample-v2-code.md).

> [!WARNING]
> Microsoft recommends you do _not_ use the ROPC flow. In most scenarios, more secure alternatives are available and recommended. This flow requires a very high degree of trust in the application, and carries risks which are not present in other flows. You should only use this flow when other more secure flows can't be used.

> [!IMPORTANT]
>
> * The Microsoft identity platform endpoint only supports ROPC for Azure AD tenants, not personal accounts. Это означает, что придется использовать конечную точку клиента (`https://login.microsoftonline.com/{TenantId_or_Name}`) или конечную точку `organizations`.
> * Пользователи личных учетных записей, приглашенные в клиент Azure AD, не могут использовать ROPC.
> * Пользователи учетных записей без пароля не смогут войти в систему через ROPC. Для таких случаев мы рекомендуем использовать для приложения другой поток.
> * Если пользователи должны использовать для входа в приложение многофакторную проверку подлинности (MFA), они будут заблокированы.
> * ROPC is not supported in [hybrid identity federation](/azure/active-directory/hybrid/whatis-fed) scenarios (for example, Azure AD and ADFS used to authenticate on-premises accounts). If users are full-page redirected to an on-premises identity providers, Azure AD is not able to test the username and password against that identity provider. [Pass-through authentication](/azure/active-directory/hybrid/how-to-connect-pta) is supported with ROPC, however.

## <a name="protocol-diagram"></a>Схема протокола

На следующей схеме показано представление потока ROPC.

![Diagram showing the resource owner password credential flow](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Запрос авторизации

The ROPC flow is a single request: it sends the client identification and user's credentials to the IDP, and then receives tokens in return. Перед этим клиент должен получить от пользователя адрес электронной почты (или другое имя участника-пользователя) и пароль. Сразу после успешного выполнения запроса клиент обязан безопасно удалить из памяти учетные данные пользователя. Ни в коем случае нельзя сохранять их.

> [!TIP]
> Попытайтесь выполнить этот запрос в Postman.
> [![Try running this request in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required. 

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Параметр | Условие | Описание |
| --- | --- | --- |
| `tenant` | Обязательно для заполнения | Клиент каталога, в который пользователь выполняет вход. Его можно указать в виде GUID или понятного имени. Этот параметр не может иметь значение `common` или `consumers`, но может быть равен `organizations`. |
| `client_id` | Обязательно для заполнения | The Application (client) ID that the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page assigned to your app. | 
| `grant_type` | Обязательно для заполнения | Нужно задать значение `password`. |
| `username` | Обязательно для заполнения | Адрес электронной почты пользователя. |
| `password` | Обязательно для заполнения | Пароль пользователя. |
| `scope` | Рекомендуется | Разделенный пробелами список [областей](v2-permissions-and-consent.md) или разрешений, которые нужны приложению. In an interactive flow, the admin or the user must consent to these scopes ahead of time. |
| `client_secret`| Sometimes required | If your app is a public client, then the `client_secret` or `client_assertion` cannot be included.  If the app is a confidential client, then it must be included. | 
| `client_assertion` | Sometimes required | A different form of `client_secret`, generated using a certificate.  See [certificate credentials](active-directory-certificate-credentials.md) for more details. | 

### <a name="successful-authentication-response"></a>Успешный ответ аутентификации

The following example shows a successful token response:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Параметр | Формат | Описание |
| --------- | ------ | ----------- |
| `token_type` | Строка | Всегда имеет значение `Bearer`. |
| `scope` | Строки, разделенные пробелами | Если возвращен маркер доступа, этот параметр содержит список областей, для которых действует этот маркер. |
| `expires_in`| int | Количество секунд, в течение которых действует предоставленный маркер доступа. |
| `access_token`| Непрозрачная строка | Выдается для запрошенных [областей](v2-permissions-and-consent.md). |
| `id_token` | JWT | Выдается, если исходный параметр `scope` содержит область `openid`. |
| `refresh_token` | Непрозрачная строка | Выдается, если исходный параметр `scope` содержит `offline_access`. |

Вы можете использовать маркер обновления для получения новых маркеров доступа и маркеров обновления с помощью того же потока, который описан в [документации по потоку кода OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Сообщение об ошибке

Если пользователь не указал правильное имя пользователя или пароль, либо клиент не получил требуемое согласие, аутентификация завершается ошибкой.

| Ошибка | Описание | Действие клиента |
|------ | ----------- | -------------|
| `invalid_grant` | Сбой аутентификации | Учетные данные указаны неверно или у клиента отсутствует согласие для запрошенных областей. If the scopes aren't granted, a `consent_required` error will be returned. В этом случае клиент должен перенаправить пользователя в интерактивный интерфейс через веб-представление или браузер. |
| `invalid_request` | Запрос был неправильно сформирован | The grant type isn't supported on the `/common` or `/consumers` authentication contexts.  Use `/organizations` or a tenant ID instead. |

## <a name="learn-more"></a>Дополнительные сведения

* Вы можете опробовать ROPC самостоятельно с помощью [примера консольного приложения](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* To determine whether you should use the v2.0 endpoint, read about [Microsoft identity platform limitations](active-directory-v2-limitations.md).
