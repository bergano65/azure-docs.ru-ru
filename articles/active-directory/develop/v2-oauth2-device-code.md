---
title: Use Microsoft identity platform to sign in users on browser-less devices | Azure
description: Build embedded and browser-less authentication flows using the device authorization grant.
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
ms.openlocfilehash: 9c948c59a90e0db17b4704188221cfc3c3d82310
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207603"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft identity platform and the OAuth 2.0 device authorization grant flow

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

The Microsoft identity platform supports the [device authorization grant](https://tools.ietf.org/html/rfc8628), which allows users to sign in to input-constrained devices such as a smart TV, IoT device, or printer.  Чтобы включить этот поток на устройстве, пользователь должен выполнить вход, посетив веб-страницу в браузере на другом устройстве.  Когда пользователь выполнит вход, устройство сможет получить необходимые маркеры доступа и маркеры обновления.  

This article describes how to program directly against the protocol in your application.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](sample-v2-code.md).

> [!NOTE]
> The Microsoft identity platform endpoint doesn't support all Azure Active Directory scenarios and features. To determine whether you should use the Microsoft identity platform endpoint, read about [Microsoft identity platform limitations](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Схема протокола

Весь поток кода устройства аналогичен приведенному на следующей схеме. Описание каждого шага приведено далее в этой статье.

![Поток кода устройства](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Запрос авторизации устройства

The client must first check with the authentication server for a device and user code that's used to initiate authentication. Клиент собирает этот запрос из конечной точки `/devicecode`. В этом запросе клиент также должен добавить разрешения, которые ему требуется получить от пользователя. С момента отправки запроса у пользователя есть только 15 минут для входа (обычное значение для `expires_in`), поэтому выполняйте этот запрос, только когда пользователь указал, что готов выполнить вход.

> [!TIP]
> Попытайтесь выполнить этот запрос в Postman.
> [![Try running this request in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Параметр | Условие | Описание |
| --- | --- | --- |
| `tenant` | Обязательно для заполнения | Can be /common, /consumers, or /organizations.  It can also be the directory tenant that you want to request permission from in GUID or friendly name format.  |
| `client_id` | Обязательно для заполнения | The **Application (client) ID** that the [Azure portal – App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) experience assigned to your app. |
| `scope` | Рекомендуется | Разделенный пробелами список [областей](v2-permissions-and-consent.md) , для которого необходимо согласие пользователя.  |

### <a name="device-authorization-response"></a>Ответ авторизации устройства

При успешном ответе возвращается объект JSON, содержащий необходимые сведения, чтобы разрешить пользователю войти в систему.  

| Параметр | Формат | Описание |
| ---              | --- | --- |
|`device_code`     | Строка | Длинная строка, используемая для проверки сеанса между клиентом и сервером авторизации. The client uses this parameter to request the access token from the authorization server. |
|`user_code`       | Строка | A short string shown to the user that's used to identify the session on a secondary device.|
|`verification_uri`| URI | URI, по которому пользователь должен перейти с помощью `user_code` для входа. |
|`expires_in`      | int | Количество секунд до окончания срока действия `device_code` и `user_code`. |
|`interval`        | int | Число секунд ожидания клиентом между опрашивающими запросами. |
| `message`        | Строка | Понятная пользователю строка с инструкциями. Ее можно локализовать, включив **параметр запроса** в запросе формы `?mkt=xx-XX`, заменив соответствующий код языка и региональных параметров. |

> [!NOTE]
> The `verification_uri_complete` response field is not included or supported at this time.  We mention this because if you read the [standard](https://tools.ietf.org/html/rfc8628) you see that `verification_uri_complete` is listed as an optional part of the device code flow standard.

## <a name="authenticating-the-user"></a>Проверка подлинности пользователя

After receiving the `user_code` and `verification_uri`, the client displays these to the user, instructing them to sign in using their mobile phone or PC browser.

If the user authenticates with a personal account (on /common or /consumers), they will be asked to sign in again in order to transfer authentication state to the device.  They will also be asked to provide consent, to ensure they are aware of the permissions being granted.  This does not apply to work or school accounts used to authenticate. 

Хотя пользователь проходит проверку подлинности в `verification_uri`, клиент должен опросить конечную точку `/token` на наличие запрошенного токена с помощью `device_code`.

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Параметр | Обязательно для заполнения | Описание|
| -------- | -------- | ---------- |
| `tenant`  | Обязательно для заполнения | The same tenant or tenant alias used in the initial request. | 
| `grant_type` | Обязательно для заполнения | Должен содержать значение `urn:ietf:params:oauth:grant-type:device_code`.|
| `client_id`  | Обязательно для заполнения | Должен соответствовать `client_id`, используемому в первоначальном запросе. |
| `device_code`| Обязательно для заполнения | `device_code`, возвращаемый в запросе авторизации устройства.  |

### <a name="expected-errors"></a>Ожидаемые ошибки

The device code flow is a polling protocol so your client must expect to receive errors before the user has finished authenticating.  

| Ошибка | Описание | Действие клиента |
| ------ | ----------- | -------------|
| `authorization_pending` | The user hasn't finished authenticating, but hasn't canceled the flow. | Повторите запрос не менее чем через `interval` с. |
| `authorization_declined` | Пользователь отклонил запрос авторизации.| Остановка опроса и возврат в состояние без проверки подлинности.  |
| `bad_verification_code`| The `device_code` sent to the `/token` endpoint wasn't recognized. | Убедитесь, что клиент отправляет правильный `device_code` в запросе. |
| `expired_token` | Прошло не менее `expires_in` секунд, и проверка подлинности больше невозможна с помощью этого `device_code`. | Stop polling and revert to an unauthenticated state. |   

### <a name="successful-authentication-response"></a>Успешный ответ аутентификации

Успешный ответ токена выглядит следующим образом:

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
| `token_type` | Строка| Всегда "Bearer". |
| `scope` | Строки, разделенные пробелами | Если маркер доступа возвращен, этот параметр приводит список областей, в которых маркер доступа является допустимым. |
| `expires_in`| int | Количество секунд, в течение которых маркер доступа является допустимым. |
| `access_token`| Непрозрачная строка | Выдается для запрошенных [областей](v2-permissions-and-consent.md).  |
| `id_token`   | JWT | Выдается, если исходный параметр `scope` содержит область `openid`.  |
| `refresh_token` | Непрозрачная строка | Выдается, если исходный параметр `scope` содержит `offline_access`.  |

You can use the refresh token to acquire new access tokens and refresh tokens using the same flow documented in the [OAuth Code flow documentation](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
