---
title: Python ADAL в руководство по миграции MSAL (ru) Azure
description: Узнайте, как перенести приложение Azure Active Directory Authentication Library (ADAL) Python в библиотеку подлинности Майкрософт (MSAL) для Python.
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: rayluo, nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: fe9dc6c04fe033fd518218d1b5ea971e573405fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696567"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>ADAL к MSAL руководство по миграции для Python

В этой статье освещаются изменения, необходимые для переноса приложения, использующее Библиотеку подлинности активных каталогов Azure (ADAL) для использования библиотеки подлинности Майкрософт (MSAL).

## <a name="difference-highlights"></a>Основные моменты различий

ADAL работает с конечным пунктом Azure Active Directory (Azure AD) v1.0. Библиотека аутентификации Майкрософт (MSAL) работает с платформой идентификации Майкрософт, ранее известной как Azure Active Directory v2.0. Платформа идентификации Майкрософт отличается от Azure AD v1.0 тем, что она:

Поддерживаются следующие возможности:
  - пользователи с рабочими и учебными учетными записями (учетные записи, подготовленные в Azure AD);
  - пользователи с личными учетными записями (например, Outlook.com или Hotmail.com);
  - клиенты, которые используют собственные адреса электронной почты или удостоверения социальных сетей (например, LinkedIn, Facebook, Google) через предложение Azure AD B2C.

- Совместимы ли стандарты с:
  - OAuth v2.0
  - OpenID Connect (OIDC)

Посмотрите, [чем отличается конечная точка microsoft (v2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

### <a name="scopes-not-resources"></a>Области вместо ресурсов

ADAL Python приобретает токены для ресурсов, но MSAL Python приобретает токены для областей. Поверхность API в MSAL Python больше не имеет параметра ресурсов. Необходимо будет предоставить области в виде списка строк, декларированных желаемых разрешений и запрошенных ресурсов. Чтобы увидеть некоторые примеры [Microsoft Graph's scopes](https://docs.microsoft.com/graph/permissions-reference)областей, см.

### <a name="error-handling"></a>Обработка ошибок

Библиотека подлинности активных каталогов Azure Active Directory Library (ADAL) для Python использует исключение, `AdalError` чтобы указать, что возникла проблема. ВМЕСТО этого MSAL для Python обычно использует коды ошибок. Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python)

### <a name="api-changes"></a>Изменения в API

В следующей таблице перечислен API в ADAL для Python и тот, который можно использовать на своем месте в MSAL для Python:

| ADAL для API Python  | MSAL для API Python |
| ------------------- | ---------------------------------- |
| [АутентификацияКонтекст](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [Публичноеприложение для клиентов или конфиденциальноеприложение клиента](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| Недоступно  | [get_authorization_request_url()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | Недоступно |
| [acquire_user_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) и [cancel_request_to_get_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) и [acquire_token_with_client_certificate()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| Недоступно | [acquire_token_on_behalf_of()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [ТокенКэш()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableToCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| Недоступно | Кэш с упорством, доступный из [MSAL Extensions](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Перенести существующие маркеры обновления для MSAL Python

Библиотека аутентификации Майкрософт (MSAL) абстрагирует концепцию токенов обновления. MSAL Python предоставляет кэш маркеров в памяти по умолчанию, так что вам не нужно хранить, искать или обновлять маркеры обновления. Пользователи также будут видеть меньше запросов в ввод, потому что маркеры обновления обычно могут обновляться без вмешательства пользователя. Для получения дополнительной информации о [Custom token cache serialization in MSAL for Python](msal-python-token-cache-serialization.md)кэше токенов см.

Следующий код поможет вам перенести токены обновления, управляемые другой библиотекой OAuth2 (включая, но не ограничиваемый ADAL Python), для управления MSAL для Python. Одной из причин миграции этих маркеров обновления является предотвращение повторного ввода существующих пользователей при переносе приложения в MSAL для Python.

Метод омрачящий токен обновления заключается в использовании MSAL для Python для приобретения нового токена доступа с помощью предыдущего маркера обновления. При возврате нового маркера обновления MSAL для Python будет хранить его в кэше. Вот пример того, как это сделать:

```python
from msal import PublicClientApplication

def get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    raise NotImplementedError("You will need to implement this by yourself")

app = PublicClientApplication(..., token_cache=...)

for old_rt, old_scope in get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    # Assuming the old scope could be a space-delimited string.
    # MSAL expects a list, like ["scope1", "scope2"].
    scopes = old_scope.split()
        # If your old refresh token came from ADAL for Python, which uses a resource rather than a scope,
        # you need to convert your v1 resource into v2 scopes
        # See https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison#scopes-not-resources
        # You may be able to append "/.default" to your v1 resource to form a scope
        # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    result = app.client.obtain_token_by_refresh_token(old_rt, scope=scopes)
    # When this call returns the new token(s), a new refresh token is issued by the Microsoft identity platform and MSAL for Python
    # stores it in the token cache.
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о сравнении версий 1.0 и 2.0 см. [здесь](active-directory-v2-compare.md).
