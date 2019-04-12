---
title: Использование платформы удостоверений для входа пользователей с помощью ROPC | Azure
description: Поддержка внебраузерных потоков аутентификации с помощью предоставления пароля владельца ресурса.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c1372263bfa3f684d30ad583bfb6a9d434c3cc2
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59499943"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credential"></a>Платформа Microsoft identity и пароль владельца ресурса OAuth 2.0

Поддерживает платформа Microsoft identity [предоставить учетные данные пароля владельца ресурса (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), который позволяет приложению вход пользователя непосредственно обрабатывая свой пароль. Поток ROPC требует высокого уровня доверия и повышает уязвимость пользователя, поэтому разработчикам следует его использовать только в тех случаях, когда совершенно невозможно использовать другие, более безопасные потоки.

> [!IMPORTANT]
> * Конечная точка платформы удостоверений Microsoft поддерживает только ROPC для клиентов Azure AD, а не личных учетных записей. Это означает, что придется использовать конечную точку клиента (`https://login.microsoftonline.com/{TenantId_or_Name}`) или конечную точку `organizations`.
> * Пользователи личных учетных записей, приглашенные в клиент Azure AD, не могут использовать ROPC.
> * Пользователи учетных записей без пароля не смогут войти в систему через ROPC. Для таких случаев мы рекомендуем использовать для приложения другой поток.
> * Если пользователи должны использовать для входа в приложение многофакторную проверку подлинности (MFA), они будут заблокированы.

## <a name="protocol-diagram"></a>Схема протокола

На следующей схеме показано представление потока ROPC.

![Поток ROPC](media/v2-oauth2-ropc/v2-oauth-ropc.png)

## <a name="authorization-request"></a>Запрос авторизации

Весь поток ROPC выполняется в рамках одного запроса &mdash; он отправляет поставщику удостоверений идентификатор клиента и учетные данные пользователя, а в ответ получает от него маркеры. Перед этим клиент должен получить от пользователя адрес электронной почты (или другое имя участника-пользователя) и пароль. Сразу после успешного выполнения запроса клиент обязан безопасно удалить из памяти учетные данные пользователя. Ни в коем случае нельзя сохранять их.

> [!TIP]
> Попытайтесь выполнить этот запрос в Postman.
> [![Выполнить в Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.

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
| `grant_type` | Обязательно для заполнения | Нужно задать значение `password`. |
| `username` | Обязательно для заполнения | Адрес электронной почты пользователя. |
| `password` | Обязательно для заполнения | Пароль пользователя. |
| `scope` | Рекомендуется | Разделенный пробелами список [областей](v2-permissions-and-consent.md) или разрешений, которые нужны приложению. Для этих областей необходимо заранее предоставить согласие администратора или пользователя через любой интерактивный поток. |

### <a name="successful-authentication-response"></a>Успешный ответ аутентификации

Ниже приведен пример ответа при успешном получении маркера.

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

| Ошибка | ОПИСАНИЕ | Действие клиента |
|------ | ----------- | -------------|
| `invalid_grant` | Сбой аутентификации | Учетные данные указаны неверно или у клиента отсутствует согласие для запрошенных областей. Если области не предоставлены, `consent_required` будет возвращена ошибка. В этом случае клиент должен перенаправить пользователя в интерактивный интерфейс через веб-представление или браузер. |
| `invalid_request` | Запрос был неправильно сформирован | Тип предоставления не поддерживается на `/common` или `/consumers` контекстов проверки подлинности.  Используйте вместо этого `/organizations`. |
| `invalid_client` | Приложение неправильно настроено | Это может произойти, если `allowPublicClient` свойства не задано значение true в [манифест приложения](reference-app-manifest.md). Свойство `allowPublicClient` является обязательным, так как поток ROPC не может использовать URI перенаправления. Если это свойство не установлено, AAD не сможет определить, является ли приложение общедоступным или конфиденциальным клиентским приложением. Не забывайте, что ROPC поддерживается только для общедоступных клиентских приложений. |

## <a name="learn-more"></a>Подробнее

* Вы можете опробовать ROPC самостоятельно с помощью [примера консольного приложения](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Чтобы определить, следует ли использовать конечную точку версии 2.0, ознакомьтесь [ограничения платформы удостоверений Microsoft](active-directory-v2-limitations.md).
