---
title: Запрос маркера доступа — Azure Active Directory B2C | Документация Майкрософт
description: Узнайте, как запросить маркер доступа из Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 042c4fa18ce583f714bbe71f522b1f8f1af3dfdb
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066125"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Запрос маркера доступа в Azure Active Directory B2C

*Маркер доступа* содержит утверждения, которые можно использовать в Azure Active Directory B2C (Azure AD B2C) для обнаружения предоставленных разрешений интерфейсам API. При вызове сервера ресурсов в HTTP-запросе должен присутствовать маркер доступа. Маркер доступа обозначается как **ACCESS_TOKEN** в ответах от Azure AD B2C.

В этой статье показано, как запросить маркер доступа для веб-приложения и веб-API. Дополнительные сведения о токенах в Azure AD B2C см. в разделе [Общие сведения о токенах в Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> **Цепочки веб-API ("от имени") не поддерживаются в Azure AD B2C.** Многие архитектуры включают веб-API, который должен вызывать другой нисходящий веб-API, защищенный Azure AD B2C. Этот сценарий распространен в клиентах, имеющих серверную веб-API, который, в свою очередь, вызывает другую службу. Этот сценарий веб-API с цепочками может поддерживаться с помощью предоставления учетных данных носителя маркера JWT OAuth 2.0 или потока On-Behalf-Of. Однако в Azure AD B2C поток On-Behalf-Of еще не реализован.

## <a name="prerequisites"></a>Предварительные требования

- [Создайте поток пользователя](tutorial-create-user-flows.md), чтобы пользователи могли зарегистрироваться и войти в ваше приложение.
- [Добавьте приложение веб-API в клиент Azure Active Directory B2C](add-web-application.md), если вы еще этого не сделали.

## <a name="scopes"></a>Области

Области предоставляют способ управления разрешениями для защищенных ресурсов. При запросе маркера доступа клиентскому приложению необходимо указать необходимые разрешения в параметре **области** запроса. Например, чтобы указать `read` **значение области** для API `https://contoso.onmicrosoft.com/api`с **URI идентификатора приложения** , областью `https://contoso.onmicrosoft.com/api/read`будет.

Области используются веб-API для реализации управления доступом на уровне области. Например, пользователи веб-API могут иметь доступ на чтение и запись или доступ только на чтение. Чтобы получить несколько разрешений в одном запросе, можно добавить несколько записей в одном параметре **области** запроса, разделяя их пробелами.

В следующем примере показаны области, декодированные в URL-адресе:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

В следующем примере показаны области, закодированные в URL-адресе:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Если запросить больше областей, чем предоставлено для клиентского приложения, вызов будет выполнен, если предоставлено по крайней мере одно разрешение. Утверждение **SCP** в результирующем маркере доступа заполняется только предоставленными разрешениями. В стандарте OpenID Connect Connect задано несколько специальных значений области. Следующие области представляют разрешение на доступ к профилю пользователя:

- **OpenID Connect** — запрашивает маркер идентификатора.
- **offline_access** — запрашивает маркер обновления с помощью [потоков кода проверки подлинности](active-directory-b2c-reference-oauth-code.md).

Если параметр **response_type** в `/authorize` запросе содержит `token`, то параметр **области** должен включать по крайней мере одну область ресурсов, отличную `offline_access` от `openid` , и которая будет предоставлена. В противном случае запрос завершится ошибкой. `/authorize`

## <a name="request-a-token"></a>Запрос маркера

Для запроса маркера доступа необходим код авторизации. Ниже приведен пример запроса к `/authorize` конечной точке для кода авторизации. Пользовательские домены не поддерживаются для использования с маркерами доступа. Используйте домен tenant-name.onmicrosoft.com в URL-адресе запроса.

В следующем примере замените следующие значения.

- `<tenant-name>` — имя вашего клиента Azure Active Directory B2C.
- `<policy-name>` — имя вашей пользовательской политики или потока пользователя.
- `<application-ID>`— Идентификатор приложения веб-приложения, зарегистрированного для поддержки потока пользователя.
- `<redirect-uri>` — **URI перенаправления**, который вы ввели при регистрации клиентского приложения.

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Ответ с кодом авторизации должен быть похож на следующий пример:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

После успешного получения кода авторизации его можно использовать для запроса маркера доступа:

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

Вы должны увидеть примерно следующий ответ:

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

При использовании https://jwt.ms для проверки возвращенного маркера доступа вы должны увидеть примерно следующий пример:

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

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о [настройке маркеров в Azure AD B2C](configure-tokens.md)
