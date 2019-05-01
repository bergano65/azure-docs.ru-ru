---
title: Запрос маркера доступа — Azure Active Directory B2C | Документация Майкрософт
description: Узнайте, как запросить маркер доступа из Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d1b4bb58d777d2dc5bd79b66e128a0edd7a88adc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702965"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Запрос маркера доступа в Azure Active Directory B2C

*Маркер доступа* содержит утверждения, что в Azure Active Directory (Azure AD) B2C можно использовать для идентификации предоставленных разрешений для API-интерфейсов. При вызове сервера ресурсов маркер доступа должен присутствовать в HTTP-запроса. Маркер доступа обозначается как **access_token** в ответы от Azure AD B2C. 

В этой статье показано, как запросить маркер доступа для веб-приложения и веб-API. Дополнительные сведения о маркерах в Azure AD B2C, см. в разделе [Обзор маркеров в Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> **Цепочки веб-API ("от имени") не поддерживаются в Azure AD B2C.** -Многие архитектуры включают веб-API, которому требуется вызывать другой подчиненный веб-API, и защищенных с помощью Azure AD B2C. Этот сценарий характерен для клиентов, имеющих серверной части веб-API, который в свою очередь вызывает другую службу. Этот сценарий веб-API с цепочками может поддерживаться с помощью предоставления учетных данных носителя маркера JWT OAuth 2.0 или потока On-Behalf-Of. Однако в Azure AD B2C поток On-Behalf-Of еще не реализован.

## <a name="prerequisites"></a>Технические условия

- [Создайте поток пользователя](tutorial-create-user-flows.md), чтобы пользователи могли зарегистрироваться и войти в ваше приложение.
- Если вы еще не сделали, [Добавление веб-приложения API в клиент Azure Active Directory B2C](add-web-application.md).

## <a name="scopes"></a>Области действия

Области позволяют управлять разрешениями к защищенным ресурсам. При запросе маркера доступа клиентское приложение должно указать нужные разрешения в **область** параметр запроса. Например, чтобы указать **значение области** из `read` для API, который имеет **URI идентификатора приложения** из `https://contoso.onmicrosoft.com/api`, нужно задать область `https://contoso.onmicrosoft.com/api/read`.

Области используются веб-API для реализации управления доступом на уровне области. Например, пользователи веб-API могут иметь доступ на чтение и запись или доступ только на чтение. Чтобы получить несколько разрешений в одном запросе, можно добавить несколько записей в одном **область** параметр запроса, разделяя их пробелами.

В следующем примере показано областей декодировать в URL-адрес:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

В следующем примере показано областей, закодированный в URL-адрес:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Если вы запрашивали дополнительные области, чем было предоставлено для клиентского приложения, вызов завершается успешно, если по крайней мере одно разрешение. **Scp** утверждения в полученный маркер доступа, заполняется только разрешения, которые были предоставлены. Стандартное утверждение OpenID Connect определяет несколько специальные области значения. Следующие области представляют право доступа к профилю пользователя:

- **openid** -запрашивает маркер идентификатора.
- **offline_access** -запрашивает маркер обновления с помощью [потоков кода аутентификации](active-directory-b2c-reference-oauth-code.md).

Если **response_type** параметр в `/authorize` запрос включает `token`, **область** параметр должен содержать по крайней мере одну область ресурса, отличное от `openid` и `offline_access`, будут предоставлены. В противном случае `/authorize` запрос завершается ошибкой.

## <a name="request-a-token"></a>Запрос маркера

Чтобы запросить маркер доступа, требуется код авторизации. Ниже приведен пример запроса на `/authorize` конечную точку для кода авторизации. Пользовательские домены не поддерживаются для использования с помощью маркеров доступа. Используйте домен name.onmicrosoft.com клиента в URL-АДРЕСЕ запроса.

В следующем примере замените следующие значения.

- `<tenant-name>` — имя вашего клиента Azure Active Directory B2C.
- `<policy-name>` — имя вашей пользовательской политики или потока пользователя.
- `<application-ID>` — Идентификатор приложения веб-приложения, зарегистрированный для поддержки потока пользователя.
- `<redirect-uri>` — **URI перенаправления**, который вы ввели при регистрации клиентского приложения.

```
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://tenant-name>.onmicrosoft.com/api/read
&response_type=code 
```

Ответ с кодом авторизации должен быть как в этом примере:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

После успешного получения кода авторизации, его можно использовать для запроса маркера доступа:

```
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: https://<tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Вы должны увидеть примерно следующим образом:

```
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

При использовании https://jwt.ms для проверки, возвращенный маркер доступа, вы увидите, что-то подобное приведенному ниже:

```
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

- Дополнительные сведения о том, как [Настройка маркеров в Azure AD B2C](configure-tokens.md)
