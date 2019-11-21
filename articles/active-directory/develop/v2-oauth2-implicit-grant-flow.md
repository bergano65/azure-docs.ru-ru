---
title: Secure single-page applications using the Microsoft identity platform implicit flow | Azure
description: Building web applications using Microsoft identity platform implementation of the implicit flow for single-page apps.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
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
ms.openlocfilehash: 1775106d7f8de9f6bbc2d9a36114e5bfda2625cb
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207627"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft identity platform and Implicit grant flow

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

With the Microsoft identity platform endpoint, you can sign users into your single-page apps with both personal and work or school accounts from Microsoft. Во время проверки подлинности одностраничные и другие приложения JavaScript, которые запускаются в основном в браузере, сталкиваются с некоторыми проблемами.

* Характеристики безопасности этих приложений значительно отличаются от традиционных серверных веб-приложений.
* Многие серверы авторизации и поставщики удостоверений не поддерживают запросы CORS.
* Полностраничный браузер перенаправляет пользователя из приложения, взаимодействие с которым становится особенно агрессивным.

For these applications (AngularJS, Ember.js, React.js, and so on), Microsoft identity platform supports the OAuth 2.0 Implicit Grant flow. Подробное описание неявного потока данных см. в [спецификации OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). Its primary benefit is that it allows the app to get tokens from Microsoft identity platform without performing a backend server credential exchange. Так, приложение может авторизовать пользователей, поддерживать сеансы и получать маркеры для других веб-API — и все это из клиентского кода JavaScript. Во время использования неявного потока данных необходимо обращать внимание на некоторые важные вопросы безопасности, касающиеся [клиента](https://tools.ietf.org/html/rfc6749#section-10.3) и [маскировки под другого пользователя](https://tools.ietf.org/html/rfc6749#section-10.3).

This article describes how to program directly against the protocol in your application.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](sample-v2-code.md).

Но в одностраничном приложении можно обойтись без использования библиотеки. Отправлять сообщения протокола в таком случае нужно самостоятельно. Для этого выполните следующие шаги.

> [!NOTE]
> Not all Azure Active Directory (Azure AD) scenarios and features are supported by the Microsoft identity platform endpoint. To determine if you should use the Microsoft identity platform endpoint, read about [Microsoft identity platform limitations](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Схема протокола

На следующей схеме показано, как выглядит весь неявный входной поток. В последующих разделах каждый шаг описывается более подробно.

![Diagram showing the implicit sign-in flow](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Отправка запроса на вход

To initially sign the user into your app, you can send an [OpenID Connect](v2-protocols-oidc.md) authentication request and get an `id_token` from the Microsoft identity platform endpoint.

> [!IMPORTANT]
> To successfully request an ID token and/or an access token, the app registration in the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page must have the corresponding implicit grant flow enabled, by selecting **ID tokens** and.or **access tokens** under the **Implicit grant** section. If it's not enabled, an `unsupported_response` error will be returned: **The provided value for the input parameter 'response_type' is not allowed for this client. Expected value is 'code'**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> To test signing in using the implicit flow, click <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> After signing in, your browser should be redirected to `https://localhost/myapp/` with an `id_token` in the address bar.
>

| Параметр |  | Описание |
| --- | --- | --- |
| `tenant` | обязательно |Значение `{tenant}` в пути запроса можно использовать для того, чтобы контролировать, кто может входить в приложение. Допустимые значения: `common`, `organizations`, `consumers`, а также идентификаторы клиента. Дополнительные сведения см. в [описании протоколов](active-directory-v2-protocols.md#endpoints). |
| `client_id` | обязательно | The Application (client) ID that the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page assigned to your app. |
| `response_type` | обязательно |Должен включать `id_token` для входа в OpenID Connect. Этот параметр также может содержать значение `token` параметра response_type (тип ответа). Использование `token` здесь позволяет приложению получать токен доступа непосредственно от конечной точки авторизации, при этом отправлять новый запрос на вход в эту конечную точку не требуется. If you use the `token` response_type, the `scope` parameter must contain a scope indicating which resource to issue the token for (for example, user.read on Microsoft Graph).  |
| `redirect_uri` | рекомендуется |URI перенаправления приложения, на который можно отправлять ответы проверки подлинности для их получения приложением. Он должен в точности соответствовать одному из URI перенаправления, зарегистрированных на портале, но иметь форму закодированного URL-адреса. |
| `scope` | обязательно |Список [областей](v2-permissions-and-consent.md) с разделителями-пробелами. For OpenID Connect (id_tokens), it must include the scope `openid`, which translates to the "Sign you in" permission in the consent UI. Optionally you may also want to include the `email` and `profile` scopes for gaining access to additional user data. You may also include other scopes in this request for requesting consent to various resources, if an access token is requested. |
| `response_mode` | необязательный |Указывает метод, с помощью которого результирующий маркер будет отправлен приложению. Defaults to query for just an access token, but fragment if the request includes an id_token. |
| `state` | рекомендуется |Значение, включенное в запрос, которое также возвращается в ответе токена. Это может быть строка любого контента. Как правило, для [предотвращения подделки межсайтовых запросов](https://tools.ietf.org/html/rfc6749#section-10.12)используется генерируемое случайным образом уникальное значение. Параметр "state" также используется для кодирования информации о состоянии пользователя в приложении перед созданием запроса на проверку подлинности, например информации об открытой на тот момент странице или представлении. |
| `nonce` | обязательно |Значение, включенное в запрос и созданное приложением, которое войдет в состав полученного токена "id_token" в качестве утверждения. Приложение может проверить это значение во избежание атак с использованием воспроизведения токена. Это значение обычно представляет собой случайную уникальную строку, которую можно использовать для определения источника запроса. Требуется только при запросе id_token. |
| `prompt` | необязательный |Указывает требуемый тип взаимодействия с пользователем. На текущий момент единственные допустимые значения — login, none, select_account и consent. При значении `prompt=login` пользователю придется вводить учетные данные по запросу. Единый вход не сработает. `prompt=none` is the opposite - it will ensure that the user isn't presented with any interactive prompt whatsoever. If the request can't be completed silently via single-sign on, the Microsoft identity platform endpoint will return an error. `prompt=select_account` отправляет пользователя в средство выбора учетных записей, где будут отображаться все учетные записи, запомненные в сеансе. Если установить значение `prompt=consent`, то после входа пользователь увидит диалоговое окно согласия OAuth с запросом на предоставление разрешений приложению. |
| `login_hint`  |необязательный |Можно применять для предварительного заполнения поля имени пользователя или электронного адреса на странице входа пользователя (если имя пользователя известно заранее). Зачастую этот параметр используется в приложениях при повторной проверке подлинности. При этом имя пользователя извлекается во время предыдущего входа с помощью утверждения `preferred_username`.|
| `domain_hint` | необязательный |If included, it will skip the email-based discovery process that user goes through on the sign in page, leading to a slightly more streamlined user experience. This is commonly used for Line of Business apps that operate in a single tenant, where they will provide a domain name within a given tenant.  This will forward the user to the federation provider for that tenant.  Note that this will prevent guests from signing into this application.  |

На текущем этапе пользователю придется ввести учетные данные и завершить проверку подлинности. The Microsoft identity platform endpoint will also ensure that the user has consented to the permissions indicated in the `scope` query parameter. Если пользователь **не предоставил** какие-либо из этих разрешений, конечная точка запросит их у пользователя. Дополнительные сведения см. в статье [Разрешения и предоставление согласия в конечной точке Azure Active Directory версии 2.0](v2-permissions-and-consent.md).

Once the user authenticates and grants consent, the Microsoft identity platform endpoint will return a response to your app at the indicated `redirect_uri`, using the method specified in the `response_mode` parameter.

#### <a name="successful-response"></a>Успешный ответ

Успешный ответ с использованием `response_mode=fragment` и `response_type=id_token+token` выглядит следующим образом (разрывы строк — для удобства чтения):

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Параметр | Описание |
| --- | --- |
| `access_token` |Указывается, если параметр `response_type` содержит значение `token`. Маркер доступа, запрошенный приложением. The access token shouldn't be decoded or otherwise inspected, it should be treated as an opaque string. |
| `token_type` |Указывается, если параметр `response_type` содержит значение `token`. Всегда будет использоваться значение `Bearer`. |
| `expires_in`|Указывается, если параметр `response_type` содержит значение `token`. Указывает количество секунд, в течение которых маркер остается допустимым (для кэширования). |
| `scope` |Указывается, если параметр `response_type` содержит значение `token`. Указывает области, для которых будет допустимым access_token. May not include all of the scopes requested, if they were not applicable to the user (in the case of Azure AD-only scopes being requested when a personal account is used to log in). |
| `id_token` | Подписанный JSON Web Token (JWT). Приложение может декодировать сегменты этого токена, чтобы запрашивать сведения о пользователе, выполнившем вход. The  app can cache the values and display them, but it shouldn't rely on them for any authorization or security boundaries. См. дополнительные сведения о маркерах id_token: [`id_token reference`](id-tokens.md). <br> **Примечание.** Предоставляется, только если подан запрос на область `openid`. |
| `state` |Если запрос содержит параметр "state", в ответе должно отображаться то же значение. Приложение должно проверить, совпадают ли значения параметра "state" в запросе и ответе. |

#### <a name="error-response"></a>Сообщение об ошибке

Сообщения об ошибках также можно отправлять на `redirect_uri` , чтобы приложение обрабатывало их должным образом:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Параметр | Описание |
| --- | --- |
| `error` |Строка кода ошибки, которую можно использовать для классификации типов возникающих ошибок и реагирования на них. |
| `error_description` |Конкретное сообщение об ошибке, с помощью которого разработчик может определить причину возникновения ошибки проверки подлинности. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Getting access tokens silently in the background

Now that you've signed the user into your single-page app, you can silently get access tokens for calling web APIs secured by Microsoft identity platform, such as the [Microsoft Graph](https://developer.microsoft.com/graph). Даже если вы уже получили токен с использованием параметра response_type со значением `token`, этот метод можно использовать для получения токенов к дополнительным ресурсам. При наличии этих токенов пользователям не нужно повторно выполнять вход.

In the normal OpenID Connect/OAuth flow, you would do this by making a request to the Microsoft identity platform `/token` endpoint. However, the Microsoft identity platform endpoint does not support CORS requests, so making AJAX calls to get and refresh tokens is out of the question. Вместо этого для получения новых маркеров для других веб-API можно использовать неявный поток данных в скрытом iframe. 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Дополнительные сведения о параметрах запроса в URL-адресе см. в разделе [Отправка запроса на вход](#send-the-sign-in-request).

> [!TIP]
> Попробуйте скопировать и вставить запрос, показанный ниже, на вкладку браузера. (Не забудьте заменить значения `login_hint` на правильное значение для вашего пользователя.)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

Благодаря параметру `prompt=none` этот запрос успешно выполнится или немедленно завершится с ошибкой, и вы вернетесь к своему приложению. Успешный ответ будет отправлен в ваше приложение на указанный `redirect_uri` с помощью метода, заданного в параметре `response_mode`.

#### <a name="successful-response"></a>Успешный ответ

Успешный ответ с использованием метода `response_mode=fragment` выглядит следующим образом:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Параметр | Описание |
| --- | --- |
| `access_token` |Указывается, если параметр `response_type` содержит значение `token`. Это маркер доступа, запрошенный приложением. В данном случае для Microsoft Graph. The access token shouldn't be decoded or otherwise inspected, it should be treated as an opaque string. |
| `token_type` | Всегда будет использоваться значение `Bearer`. |
| `expires_in` | Указывает количество секунд, в течение которых маркер остается допустимым (для кэширования). |
| `scope` | Указывает области, для которых будет допустимым access_token. May not include all of the scopes requested, if they were not applicable to the user (in the case of Azure AD-only scopes being requested when a personal account is used to log in). |
| `id_token` | Подписанный JSON Web Token (JWT). Указывается, если параметр `response_type` содержит значение `id_token`. Приложение может декодировать сегменты этого токена, чтобы запрашивать сведения о пользователе, выполнившем вход. The  app can cache the values and display them, but it shouldn't rely on them for any authorization or security boundaries. Дополнительные сведения о маркерах id_token см. в статье [`id_token`Маркеры идентификаторов](id-tokens.md). <br> **Примечание.** Предоставляется, только если подан запрос на область `openid`. |
| `state` |Если запрос содержит параметр "state", в ответе должно отображаться то же значение. Приложение должно проверить, совпадают ли значения параметра "state" в запросе и ответе. |

#### <a name="error-response"></a>Сообщение об ошибке

Сообщения об ошибках также можно отправлять на `redirect_uri` , чтобы приложение правильно обрабатывало их. Если вы используете `prompt=none`, отобразится следующая ошибка.

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Параметр | Описание |
| --- | --- |
| `error` |Строка кода ошибки, которую можно использовать для классификации типов возникающих ошибок и реагирования на них. |
| `error_description` |Конкретное сообщение об ошибке, с помощью которого разработчик может определить причину возникновения ошибки проверки подлинности. |

После появления этой ошибки в запросе iframe пользователю необходимо войти еще раз в интерактивном режиме для получения нового маркера. Этот случай можно обрабатывать любым способом, который лучше всего подходит для вашего приложения.

## <a name="refreshing-tokens"></a>Обновление маркеров

Неявное предоставление не обеспечивает маркеры обновления. Срок действия маркеров `id_token` и `access_token` очень короткий, поэтому приложение должно быть готово периодически обновлять их. To refresh either type of token, you can perform the same hidden iframe request from above using the `prompt=none` parameter to control the identity platform's behavior. If you want to receive a new `id_token`, be sure to use `id_token` in the `response_type` and `scope=openid`, as well as a `nonce` parameter.

## <a name="send-a-sign-out-request"></a>Отправка запроса на выход

The OpenID Connect `end_session_endpoint` allows your app to send a request to the Microsoft identity platform endpoint to end a user's session and clear cookies set by the Microsoft identity platform endpoint. Чтобы пользователь полностью вышел из веб-приложения, приложение должно завершить свой сеанс пользователя (обычно с помощью очистки кэша маркеров или файлов cookie), а затем перенаправить браузер на:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Параметр |  | Описание |
| --- | --- | --- |
| `tenant` |обязательно |Значение `{tenant}` в пути запроса можно использовать для того, чтобы контролировать, кто может входить в приложение. Допустимые значения: `common`, `organizations`, `consumers`, а также идентификаторы клиента. Дополнительные сведения см. в [описании протоколов](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | рекомендуется | URL-адрес, на который следует возвратить пользователя после выхода. Это значение должно соответствовать одному из универсальных кодов ресурсов (URI) перенаправления, зарегистрированных для приложения. If not included, the user will be shown a generic message by the Microsoft identity platform endpoint. |

## <a name="next-steps"></a>Дальнейшие действия

* Перейдите на страницу [примеров MSAL JS](sample-v2-code.md), чтобы приступить к созданию кода.
