---
title: Руководством по миграции ADAL в MSAL для Python | Службы
description: Узнайте, как перенести приложение Python для библиотеки проверки подлинности Azure Active Directory (ADAL) в библиотеку проверки подлинности Майкрософт (MSAL) для Python.
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: henrikm
editor: twhitney
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b31000c8b5e64cbd0edb2fc062f1c6e077a75f4
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481938"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Руководством по миграции ADAL в MSAL для Python

В этой статье описываются изменения, которые необходимо внести, чтобы перенести приложение, Azure Active Directory использующее библиотеку проверки подлинности (MSAL), с помощью библиотеки аутентификации Майкрософт (ADAL).

## <a name="difference-highlights"></a>Особенности разницы

ADAL работает с конечной точкой Azure Active Directory v 1.0. Библиотека проверки подлинности Microsoft (MSAL) работает с платформой идентификации Майкрософт, которая ранее называлась конечной точкой Azure Active Directory v 2.0. Платформа Microsoft Identity отличается от Azure Active Directory v 1.0:

Поддерживает:
  - пользователи с рабочими и учебными учетными записями (учетные записи, подготовленные в Azure AD);
  - пользователи с личными учетными записями (например, Outlook.com или Hotmail.com);
  - клиенты, которые используют собственные адреса электронной почты или удостоверения социальных сетей (например, LinkedIn, Facebook, Google) через предложение Azure AD B2C.

- Совместимы со стандартами:
  - OAuth 2.0
  - OpenID Connect Connect (OIDC)

Дополнительные сведения см. [в разделе чем отличаются конечная точка платформы Microsoft Identity Platform (v 2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison) .

### <a name="scopes-not-resources"></a>Области вместо ресурсов

ADAL Python получает маркеры для ресурсов, но MSAL Python получает маркеры для областей. У поверхности API в MSAL Python больше нет параметра ресурса. Необходимо указать области в виде списка строк, которые объявляют требуемые разрешения и ресурсы. Некоторые примеры областей см. в разделе [области Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).

### <a name="error-handling"></a>Обработка ошибок

Azure Active Directory Библиотека проверки подлинности (ADAL) для Python использует исключение `AdalError`, чтобы указать, что возникла проблема. MSAL для Python обычно использует коды ошибок. Дополнительные сведения см. в разделе [MSAL for Python Error обработка ошибок](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python).

### <a name="api-changes"></a>Изменения в API

В следующей таблице приведен интерфейс API в ADAL для Python, который можно использовать в своем месте в MSAL для Python:

| ADAL для API Python  | MSAL для API Python |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [Публикклиентаппликатион или Конфидентиалклиентаппликатион](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| Недоступно  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | Недоступно |
| [acquire_user_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) и [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) и [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| Недоступно | [acquire_token_on_behalf_of ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache ()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [Сериализаблетокенкаче ()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| Недоступно | Кэширование с сохранением, доступное из [расширений MSAL](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Перенос существующих маркеров обновления для MSAL Python

Библиотека проверки подлинности Майкрософт (MSAL) абстрагирует концепцию маркеров обновления. MSAL Python по умолчанию предоставляет кэш маркеров в памяти, поэтому вам не нужно хранить, искать или обновлять маркеры обновления. Пользователи также увидят меньше запросов на вход, так как маркеры обновления обычно обновляются без вмешательства пользователя. Дополнительные сведения о кэше маркеров см. в разделе [сериализация кэша пользовательской лексемы в MSAL для Python](msal-python-token-cache-serialization.md).

Следующий код поможет перенести маркеры обновления, управляемые другой библиотекой OAuth2 (включая, но не ограниченные ADAL Python), под управление MSAL для Python. Одна из причин переноса этих маркеров обновления — предотвращение повторного входа существующих пользователей при переносе приложения в MSAL для Python.

Метод переноса маркера обновления — использование MSAL для Python для получения нового маркера доступа с помощью предыдущего маркера обновления. При возвращении нового маркера обновления MSAL для Python сохранит его в кэше. Ниже приведен пример того, как это сделать.

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

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о сравнении версий 1.0 и 2.0 см. [здесь](active-directory-v2-compare.md).
