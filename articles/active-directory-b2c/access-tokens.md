---
title: Запросить токен доступа - Активный каталог Azure B2C Документы Майкрософт
description: Узнайте, как запросить токен доступа из Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8358d3378ea892ebeef653bcb51243c9f1aa0b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259777"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Запрос токена доступа в Azure Active Directory B2C

*Конкен доступа* содержит утверждения, которые можно использовать в Azure Active Directory B2C (Azure AD B2C) для идентификации выданных разрешений на AA. При вызове сервера ресурсов в запросе HTTP должен присутствовать маркер доступа. Токен доступа обозначается как **access_token** в ответах Azure AD B2C.

В этой статье показано, как запросить токен доступа для веб-приложения и веб-aPI. Более подробную информацию о токенах в Azure AD B2C можно узнать в [обзоре токенов в Azure Active Directory B2C.](tokens-overview.md)

> [!NOTE]
> **Цепочки веб-API ("от имени") не поддерживаются в Azure AD B2C.** - Многие архитектуры включают веб-API, который должен вызывать другой нижепом веб-API, оба защищены Azure AD B2C. Этот сценарий является общим для клиентов, которые имеют веб-API задней части, которая, в свою очередь, вызывает другую службу. Этот сценарий веб-API с цепочками может поддерживаться с помощью предоставления учетных данных носителя маркера JWT OAuth 2.0 или потока On-Behalf-Of. Однако в Azure AD B2C поток On-Behalf-Of еще не реализован.

## <a name="prerequisites"></a>Предварительные требования

- [Создайте поток пользователя](tutorial-create-user-flows.md), чтобы пользователи могли зарегистрироваться и войти в ваше приложение.
- Если вы еще не сделали этого, [добавьте приложение Web API к вашему арендатору Azure Active Directory B2C.](add-web-application.md)

## <a name="scopes"></a>Области действия

Области предоставляют способ управления разрешениями защищенным ресурсам. При запросе токен доступа клиенту необходимо указать желаемые разрешения в параметре **охвата** запроса. Например, чтобы указать `read` значение области **для** API, в `https://contoso.onmicrosoft.com/api`которое есть `https://contoso.onmicrosoft.com/api/read` **ИДентификатор Приложения URI,** область будет .

Области используются веб-API для реализации управления доступом на уровне области. Например, пользователи веб-API могут иметь доступ на чтение и запись или доступ только на чтение. Чтобы получить несколько разрешений в одном запросе, можно добавить несколько записей в один параметр **области** запроса, разделенный пробелами.

В следующем примере показаны области, расшифровываемые в URL::

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

В следующем примере показаны области, закодированные в URL::

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Если вы запрашиваете больше областей, чем предоставляется для вашего клиентского приложения, вызов удается, если по крайней мере одно разрешение предоставляется. Претензия **scp** в полученном токене доступа заполняется только разрешениями, которые были успешно предоставлены. Стандарт OpenID Connect определяет несколько специальных значений сферы. Следующие области представляют собой разрешение на доступ к профилю пользователя:

- **openid** - Запрашивает токен ID.
- **offline_access** - Запросы на обновление токена с использованием [потоков Кода Аута.](authorization-code-flow.md)

Если **параметр** response_type `/authorize` в `token`запросе включает, параметр **области** должен `openid` `offline_access` включать по крайней мере один ресурс, кроме и который будет предоставлен. В противном случае `/authorize` запрос завершается неудачей.

## <a name="request-a-token"></a>Запрос маркера

Чтобы запросить токен доступа, нужен код авторизации. Ниже приведен пример запроса `/authorize` в конечную точку для кода авторизации. Пользовательские домены не поддерживаются для использования с токенами доступа. Используйте свой tenant-name.onmicrosoft.com домен в URL-адресе запроса.

В следующем примере замените следующие значения.

- `<tenant-name>` — имя вашего клиента Azure Active Directory B2C.
- `<policy-name>` — имя вашей пользовательской политики или потока пользователя.
- `<application-ID>`- Идентификатор приложения веб-приложения, зарегистрированного для поддержки пользовательского потока.
- `<redirect-uri>` — **URI перенаправления**, который вы ввели при регистрации клиентского приложения.

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Ответ с кодом авторизации должен быть аналогичен этому примеру:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

После успешного получения кода авторизации можно использовать его для запроса токена доступа:

```HTTP
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Вы должны увидеть что-то похожее на следующий ответ:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

При https://jwt.ms использовании для изучения возвращенного токена доступа следует увидеть нечто похожее на следующий пример:

```JSON
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте о том, как [настроить токены в Azure AD B2C](configure-tokens.md)
