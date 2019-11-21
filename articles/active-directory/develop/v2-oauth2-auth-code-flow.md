---
title: Microsoft identity platform and OAuth Authorization Code Flow | Azure
description: Building web applications using the Microsoft identity platform implementation of the OAuth 2.0 authentication protocol.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: a20f540d4d220d5cf7171633b817a496d48b8128
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207730"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft identity platform and OAuth 2.0 authorization code flow

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Код авторизации OAuth 2.0 может использоваться в приложениях, установленных на устройстве, для получения доступа к защищенным ресурсам, таким как веб-API. Using the Microsoft identity platform implementation of OAuth 2.0, you can add sign in and API access to your mobile and desktop apps. Для выполнения задач этого руководства не предусмотрено использование конкретного языка. Здесь объясняется, как отправлять и получать сообщения HTTP без применения [библиотек аутентификации Azure с открытым кодом](reference-v2-libraries.md).

This article describes how to program directly against the protocol in your application.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](sample-v2-code.md).

> [!NOTE]
> Not all Azure Active Directory scenarios & features are supported by the Microsoft identity platform endpoint. To determine if you should use the Microsoft identity platform endpoint, read about [Microsoft identity platform limitations](active-directory-v2-limitations.md).

Описание потока кода авторизации OAuth 2.0 см. в [разделе 4.1 спецификации OAuth 2.0](https://tools.ietf.org/html/rfc6749). It's used to perform authentication and authorization in the majority of app types, including [web apps](v2-app-types.md#web-apps) and [natively installed  apps](v2-app-types.md#mobile-and-native-apps). The flow enables apps to securely acquire access_tokens that can be used to access resources secured by the Microsoft identity platform endpoint.

## <a name="protocol-diagram"></a>Схема протокола

В общих чертах весь поток проверки подлинности для собственных или мобильных приложений можно представить следующим образом:

![Поток кода проверки подлинности OAuth](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>Запрос кода авторизации

Поток кода авторизации начинается с того, что клиент направляет пользователя к конечной точке `/authorize` . В этом запросе клиент указывает разрешения, которые ему требуется получить от пользователя:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
```

> [!TIP]
> Чтобы выполнить этот запрос, щелкните ссылку ниже! После входа браузер будет перенаправлен по адресу `https://localhost/myapp/`, при этом в адресной строке будет указано `code`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>;

| Параметр    | Обязательный/необязательный | Описание |
|--------------|-------------|--------------|
| `tenant`    | обязательно    | Значение `{tenant}` в пути запроса можно использовать для того, чтобы контролировать, кто может входить в приложение. Допустимые значения: `common`, `organizations`, `consumers`, а также идентификаторы клиента. Дополнительные сведения см. в [описании протоколов](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | обязательно    | The **Application (client) ID** that the [Azure portal – App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) experience assigned to your app.  |
| `response_type` | обязательно    | Должен содержать `code` для потока кода авторизации.       |
| `redirect_uri`  | обязательно | URI перенаправления приложения, на который можно отправлять ответы проверки подлинности для их получения приложением. Он должен в точности соответствовать одному из URI перенаправления, зарегистрированных на портале, но иметь форму закодированного URL-адреса. Для собственных и мобильных приложений следует использовать значение `https://login.microsoftonline.com/common/oauth2/nativeclient` по умолчанию.   |
| `scope`  | обязательно    | Разделенный пробелами список [областей](v2-permissions-and-consent.md) , для которого необходимо согласие пользователя.  For the `/authorize` leg of the request, this can cover multiple resources, allowing your app to get consent for multiple web APIs you want to call. |
| `response_mode`   | рекомендуется | Указывает метод, с помощью которого результирующий маркер будет отправлен приложению. Может применяться один из перечисленных ниже типов.<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` предоставляет код в качестве параметра строки запроса в URI перенаправления. If you're requesting an ID token using the implicit flow, you can't use `query` as specified in the [OpenID spec](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). If you're requesting just the code, you can use `query`, `fragment`, or `form_post`. `form_post` выполняет запрос POST, который содержит код для URI перенаправления. Дополнительные сведения см. в статье о [протоколе OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| `state`                 | рекомендуется | Значение, включенное в запрос, которое также возвращается в ответе токена. Это может быть строка любого контента. Как правило, для [предотвращения подделки межсайтовых запросов](https://tools.ietf.org/html/rfc6749#section-10.12)используется генерируемое случайным образом уникальное значение. Также в этом значении кодируются сведения о состоянии пользователя в приложении перед выполнением запроса на аутентификацию. Например, это могут быть сведения об открытой на тот момент странице или представлении. |
| `prompt`  | необязательный    | Указывает требуемый тип взаимодействия с пользователем. На текущий момент единственные допустимые значения — `login`, `none` и `consent`.<br/><br/>При значении - `prompt=login` пользователю придется вводить учетные данные по запросу. Единый вход не сработает.<br/>- `prompt=none` is the opposite - it will ensure that the user isn't presented with any interactive prompt whatsoever. If the request can't be completed silently via single-sign on, the Microsoft identity platform endpoint will return an `interaction_required` error.<br/>Если установить значение - `prompt=consent`, то после входа пользователь увидит диалоговое окно согласия OAuth с запросом на предоставление разрешений приложению. |
| `login_hint`  | необязательный    | Можно применять для предварительного заполнения поля имени пользователя или адреса электронной почты на странице входа пользователя (если имя пользователя известно заранее). Зачастую этот параметр используется в приложениях при повторной проверке подлинности. При этом имя пользователя извлекается во время предыдущего входа с помощью утверждения `preferred_username`.   |
| `domain_hint`  | необязательный    | Может использоваться значение `consumers` или `organizations`.<br/><br/>If included, it will skip the email-based discovery process that user goes through on the sign-in page, leading to a slightly more streamlined user experience. Обычно этот параметр применяется в приложениях при повторной аутентификации. Для этого значение утверждения `tid` извлекается из предыдущего сеанса входа. Если значение утверждения `tid` — `9188040d-6c67-4c5b-b112-36a304b66dad`, следует использовать `domain_hint=consumers`. Или используйте `domain_hint=organizations`.  |
| `code_challenge_method` | необязательный    | Метод, используемый для кодирования `code_verifier` в параметре `code_challenge`. Принимается одно из следующих значений:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Если этот параметр не указан, для `code_challenge` принимается формат открытого текста, если задано значение `code_challenge`. Microsoft identity platform supports both `plain` and `S256`. Дополнительные сведения см. в описании [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| `code_challenge`  | необязательный | Используется, чтобы защитить разрешения кода авторизации из собственного клиента при помощи ключа проверки для обмена кодом (PKCE). Является обязательным, если указан параметр `code_challenge_method`. Дополнительные сведения см. в описании [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

На текущем этапе пользователю придется ввести учетные данные и завершить проверку подлинности. The Microsoft identity platform endpoint will also ensure that the user has consented to the permissions indicated in the `scope` query parameter. Если пользователь не предоставил какие-либо из этих разрешений, конечная точка запросит их у пользователя. Подробные сведения о [разрешениях, согласии на предоставление и мультитенантных приложениях можно найти здесь](v2-permissions-and-consent.md).

Once the user authenticates and grants consent, the Microsoft identity platform endpoint will return a response to your app at the indicated `redirect_uri`, using the method specified in the `response_mode` parameter.

#### <a name="successful-response"></a>Успешный ответ

Успешный ответ с использованием метода `response_mode=query` выглядит следующим образом:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Параметр | Описание  |
|-----------|--------------|
| `code` | Запрашиваемый приложением код авторизации. Приложение может использовать код авторизации для запроса маркера доступа для целевого ресурса. Authorization_codes are short lived, typically they expire after about 10 minutes. |
| `state` | Если запрос содержит параметр "state", в ответе должно отображаться то же значение. Приложение должно проверить, совпадают ли значения параметра "state" в запросе и ответе. |

#### <a name="error-response"></a>Сообщение об ошибке

Сообщения об ошибках также можно отправлять на `redirect_uri` , чтобы приложение обрабатывало их должным образом:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Параметр | Описание  |
|----------|------------------|
| `error`  | Строка кода ошибки, которую можно использовать для классификации типов возникающих ошибок и реагирования на них. |
| `error_description` | Конкретное сообщение об ошибке, с помощью которого разработчик может определить причину возникновения ошибки проверки подлинности. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Коды ошибок конечной точки авторизации

В таблице ниже описаны различные коды ошибок, которые могут возвращаться в параметре `error` ответа с ошибкой.

| Код ошибки  | Описание    | Действие клиента   |
|-------------|----------------|-----------------|
| `invalid_request` | Ошибка протокола, например отсутствует обязательный параметр. | Исправьте запрос и отправьте его повторно. Это ошибка разработки, которая обычно определяется во время первоначального тестирования. |
| `unauthorized_client` | The client application isn't permitted to request an authorization code. | This error usually occurs when the client application isn't registered in Azure AD or isn't added to the user's Azure AD tenant. Приложение может отобразить пользователю запрос с инструкцией по установке приложения и его добавлению в Azure AD. |
| `access_denied`  | Владелец ресурса отказал в использовании  | The client application can notify the user that it can't proceed unless the user consents. |
| `unsupported_response_type` | Сервер авторизации не поддерживает тип ответа в запросе. | Исправьте запрос и отправьте его повторно. Это ошибка разработки, которая обычно определяется во время первоначального тестирования.  |
| `server_error`  | Сервер обнаружил непредвиденную ошибку.| Повторите запрос. Эти ошибки могут возникать в связи с временными условиями. Из клиентского приложения может поступить сообщение о том, что его ответ задерживается из-за временной ошибки. |
| `temporarily_unavailable`   | Сервер временно занят и не может обработать запрос. | Повторите запрос. The client application might explain to the user that its response is delayed because of a temporary condition. |
| `invalid_resource`  | The target resource is invalid because it does not exist, Azure AD can't find it, or it's not correctly configured. | Это означает, что ресурс не существует или не настроен в клиенте. Приложение может отобразить пользователю запрос с инструкцией по установке приложения и его добавлению в Azure AD. |
| `login_required` | Слишком много пользователей или пользователи не найдены | Клиент запросил автоматическую аутентификацию (`prompt=none`), но одного пользователя не удалось найти. Это может означать, что в сеансе активно несколько пользователей или пользователи отсутствуют. This takes into account the tenant chosen (for example, if there are two Azure AD accounts active and one Microsoft account, and `consumers` is chosen, silent authentication will work). |
| `interaction_required` | Для запроса требуется взаимодействие с пользователем. | Требуется дополнительный шаг аутентификации или предоставление согласия. Повторите запрос без `prompt=none`. |

## <a name="request-an-access-token"></a>Запрос маркера доступа

После получения кода авторизации и разрешения от пользователя вы можете применить `code` для получения `access_token` к требуемому ресурсу. Для этого отправьте запрос `POST` к конечной точке `/token`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> Попытайтесь выполнить этот запрос в Postman. (Don't forget to replace the `code`) [![Try running this request in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Параметр  | Обязательный/необязательный | Описание     |
|------------|-------------------|----------------|
| `tenant`   | обязательно   | Значение `{tenant}` в пути запроса можно использовать для того, чтобы контролировать, кто может входить в приложение. Допустимые значения: `common`, `organizations`, `consumers`, а также идентификаторы клиента. Дополнительные сведения см. в [описании протоколов](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | обязательно  | The Application (client) ID that the [Azure portal – App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page assigned to your app. |
| `grant_type` | обязательно   | Должен быть `authorization_code` для потока кода авторизации.   |
| `scope`      | обязательно   | Список областей с разделителями-пробелами. Области, запрашиваемые на этом участке, должны быть эквивалентны областям, запрашиваемым на первом участке, или являться их подмножеством. The scopes must all be from a single resource, along with OIDC scopes (`profile`, `openid`, `email`). Более подробное описание областей можно найти в разделе, посвященном [разрешениям, согласию на их предоставление и областям](v2-permissions-and-consent.md). |
| `code`          | обязательно  | Код авторизации, полученный на первом участке потока. |
| `redirect_uri`  | обязательно  | Значение redirect_uri, которое использовалось для получения кода authorization_code. |
| `client_secret` | необходим для веб-приложений | Секрет приложения, созданный на портале регистрации для приложения. You shouldn't use the application secret in a native app because client_secrets can't be reliably stored on devices. It's required for web apps and web APIs, which have the ability to store the client_secret securely on the server side.  Секрет клиента должен быть преобразован в формат URL-адреса перед отправкой.  |
| `code_verifier` | необязательный  | Это тот же параметр code_verifier, который использовался для получения кода авторизации (authorization_code). Является обязательным, если в запросе на код авторизации использовался PKCE. Дополнительные сведения см. в описании [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Успешный ответ

Успешный ответ токена выглядит следующим образом:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Параметр     | Описание   |
|---------------|------------------------------|
| `access_token`  | Запрашиваемый маркер доступа. Приложение может использовать этот маркер для аутентификации в защищенном ресурсе, таком как веб-API.  |
| `token_type`    | Указывает значение типа маркера. Единственный тип, поддерживаемый Azure AD, — носитель |
| `expires_in`    | Срок действия маркера доступа (в секундах). |
| `scope`         | Области, для которых действителен маркер доступа. |
| `refresh_token` | Токен обновления OAuth 2.0. Приложение может использовать этот маркер для получения дополнительных маркеров доступа после истечения срока действия текущего маркера доступа. Токены обновления действуют в течение долгого времени. Их можно использовать для длительного сохранения доступа к ресурсам. См. дополнительные сведения об [обновлении маркеров доступа](#refresh-the-access-token). <br> **Примечание.** Предоставляется, только если подан запрос на область `offline_access`. |
| `id_token`      | A JSON Web Token (JWT). Приложение может декодировать сегменты этого токена, чтобы запрашивать сведения о пользователе, выполнившем вход. Приложение может кэшировать и отображать значения, но оно не должно полагаться на них при авторизации или в целях безопасности. См. дополнительные сведения о маркерах id_token: [`id_token reference`](id-tokens.md). <br> **Примечание.** Предоставляется, только если подан запрос на область `openid`. |

### <a name="error-response"></a>Сообщение об ошибке

Сообщения об ошибках выглядят следующим образом:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Параметр         | Описание    |
|-------------------|----------------|
| `error`       | Строка кода ошибки, которую можно использовать для классификации типов возникающих ошибок и реагирования на них. |
| `error_description` | Конкретное сообщение об ошибке, с помощью которого разработчик может определить причину возникновения ошибки проверки подлинности. |
| `error_codes` | Список кодов ошибок, характерных для службы маркеров безопасности, которые могут помочь при диагностике.  |
| `timestamp`   | Время возникновения ошибки. |
| `trace_id`    | Уникальный идентификатор для запроса, который может помочь при диагностике. |
| `correlation_id` | Уникальный идентификатор для запроса, который может помочь при диагностике нескольких компонентов. |

### <a name="error-codes-for-token-endpoint-errors"></a>Коды ошибок конечных точек токенов

| Код ошибки         | Описание        | Действие клиента    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Ошибка протокола, например отсутствует обязательный параметр. | Исправьте запрос и отправьте его повторно.   |
| `invalid_grant`    | Недопустимый или устаревший код авторизации или средство проверки PKCE. | Повторите запрос к конечной точке `/authorize` и убедитесь, что параметр code_verifier указан правильно.  |
| `unauthorized_client` | The authenticated client isn't authorized to use this authorization grant type. | This usually occurs when the client application isn't registered in Azure AD or isn't added to the user's Azure AD tenant. Приложение может отобразить пользователю запрос с инструкцией по установке приложения и его добавлению в Azure AD. |
| `invalid_client` | Сбой проверки подлинности клиента.  | The client credentials aren't valid. Чтобы устранить проблему, администратору приложения необходимо обновить учетные данные.   |
| `unsupported_grant_type` | Сервер авторизации не поддерживает тип предоставления авторизации. | Измените тип предоставления в запросе. Ошибка этого типа должна происходить только во время разработки, и ее должны обнаружить при первоначальном тестировании. |
| `invalid_resource` | The target resource is invalid because it does not exist, Azure AD can't find it, or it's not correctly configured. | Это означает, что ресурс, если он существует, не настроен в клиенте. Приложение может отобразить пользователю запрос с инструкцией по установке приложения и его добавлению в Azure AD.  |
| `interaction_required` | Для запроса требуется взаимодействие с пользователем. К примеру, требуется дополнительный шаг проверки подлинности. | Выполните запрос снова, используя тот же ресурс.  |
| `temporarily_unavailable` | Сервер временно занят и не может обработать запрос. | Повторите запрос. The client application might explain to the user that its response is delayed because of a temporary condition. |

## <a name="use-the-access-token"></a>Использование маркера доступа

Успешно получив `access_token`, вы можете использовать токен в запросах в веб-API путем включения его в заголовок `Authorization`:

> [!TIP]
> Выполните этот запрос в Postman. (Replace the `Authorization` header first) [![Try running this request in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Обновление маркера доступа

Срок действия маркеров доступа весьма ограничен, поэтому их нужно обновлять после его истечения, чтобы продолжить пользоваться запросами. Для этого можно отправить еще один запрос `POST` на конечную точку `/token`, прибегнув к `refresh_token` вместо `code`.  Маркеры обновления действуют для всех разрешений, на которые ваш клиент уже получил согласия, например, маркер обновления выданный по запросу для `scope=mail.read` может использоваться для запроса нового маркера доступа для `scope=api://contoso.com/api/UseResource`.  

Для маркеров обновления не устанавливается определенное время существования. Обычно у маркеров обновления относительно продолжительное время существования. Но в некоторых случаях маркер обновления оказывается устаревшим или отозванным или не имеет достаточных привилегий для требуемого действия. Ваше приложение должно ожидать и правильно обрабатывать [ошибки, возвращаемые конечной точкой выдачи маркера](#error-codes-for-token-endpoint-errors). 

Although refresh tokens aren't revoked when used to acquire new access tokens, you are expected to discard the old refresh token. The [OAuth 2.0 spec](https://tools.ietf.org/html/rfc6749#section-6) says: "The authorization server MAY issue a new refresh token, in which case the client MUST discard the old refresh token and replace it with the new refresh token. The authorization server MAY revoke the old refresh token after issuing a new refresh token to the client."  

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> Попытайтесь выполнить этот запрос в Postman. (Don't forget to replace the `refresh_token`) [![Try running this request in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
> 

| Параметр     |                | Описание        |
|---------------|----------------|--------------------|
| `tenant`        | обязательно     | Значение `{tenant}` в пути запроса можно использовать для того, чтобы контролировать, кто может входить в приложение. Допустимые значения: `common`, `organizations`, `consumers`, а также идентификаторы клиента. Дополнительные сведения см. в [описании протоколов](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | обязательно    | The **Application (client) ID** that the [Azure portal – App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) experience assigned to your app. |
| `grant_type`    | обязательно    | Должен быть `refresh_token` для этого участка потока кода авторизации. |
| `scope`         | обязательно    | Список областей с разделителями-пробелами. Области, запрашиваемые на этом участке, должны быть эквивалентны областям, запрашиваемым на исходном участке запроса кода авторизации, или являться их подмножеством. If the scopes specified in this request span multiple resource server, then the Microsoft identity platform endpoint will return a token for the resource specified in the first scope. Более подробное описание областей можно найти в разделе, посвященном [разрешениям, согласию на их предоставление и областям](v2-permissions-and-consent.md). |
| `refresh_token` | обязательно    | Токен обновления, полученный на втором участке потока. |
| `client_secret` | необходим для веб-приложений | Секрет приложения, созданный на портале регистрации для приложения. It should not be used in a native  app, because client_secrets can't be reliably stored on devices. It's required for web apps and web APIs, which have the ability to store the client_secret securely on the server side. |

#### <a name="successful-response"></a>Успешный ответ

Успешный ответ токена выглядит следующим образом:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Параметр     | Описание         |
|---------------|-------------------------------------------------------------|
| `access_token`  | Запрашиваемый маркер доступа. Приложение может использовать этот маркер для аутентификации в защищенном ресурсе, таком как веб-API. |
| `token_type`    | Указывает значение типа маркера. Единственный тип, поддерживаемый Azure AD, — носитель |
| `expires_in`    | Срок действия маркера доступа (в секундах).   |
| `scope`         | Области, для которых действителен маркер доступа.    |
| `refresh_token` | Новый токен обновления OAuth 2.0. Вам следует заменить старый токен обновления вновь полученным, чтобы обеспечить максимальный срок действия токенов обновления. <br> **Примечание.** Предоставляется, только если подан запрос на область `offline_access`.|
| `id_token`      | Неподписанный веб-маркер JSON (JWT). Приложение может декодировать сегменты этого токена, чтобы запрашивать сведения о пользователе, выполнившем вход. Приложение может кэшировать и отображать значения, но оно не должно полагаться на них при авторизации или в целях безопасности. См. дополнительные сведения о маркерах id_token: [`id_token reference`](id-tokens.md). <br> **Примечание.** Предоставляется, только если подан запрос на область `openid`. |

#### <a name="error-response"></a>Сообщение об ошибке

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Параметр         | Описание                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Строка кода ошибки, которую можно использовать для классификации типов возникающих ошибок и реагирования на них. |
| `error_description` | Конкретное сообщение об ошибке, с помощью которого разработчик может определить причину возникновения ошибки проверки подлинности.           |
| `error_codes` |Список кодов ошибок, характерных для службы маркеров безопасности, которые могут помочь при диагностике. |
| `timestamp` | Время возникновения ошибки. |
| `trace_id` | Уникальный идентификатор для запроса, который может помочь при диагностике. |
| `correlation_id` | Уникальный идентификатор для запроса, который может помочь при диагностике нескольких компонентов. |

Описание кодов ошибок и рекомендуемых действий в клиенте см. в разделе [Коды ошибок конечных точек токенов](#error-codes-for-token-endpoint-errors).
