---
title: Ошибки и исключения (MSAL)
titleSuffix: Microsoft identity platform
description: Узнайте, как обрабатывать ошибки и исключения, условный доступ и проблемы с претензиями в приложениях MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 93d07ab1740da68298478ae2dcc2ab46d8d8362e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884024"
---
# <a name="handle-msal-exceptions-and-errors"></a>Обработка исключений и ошибок MSAL

В этой статье приводится обзор различных типов ошибок и рекомендаций по обработке распространенных ошибок, всданных в систему.

## <a name="msal-error-handling-basics"></a>Основы обработки ошибок MSAL

Исключения в библиотеке подлинности Microsoft (MSAL) предназначены для разработчиков приложений для устранения неполадок, а не для отображения конечным пользователям. Сообщения об исключениях не локализованы.

При обработке ошибок и исключений вы можете различать их по типам исключений и кодам ошибки.  Список кодов ошибок аутентификации и авторизации можно найти [здесь](reference-aadsts-error-codes.md).

Во время вхождения в систему могут возникнуть ошибки в отношении согласия, условного доступа (MFA, управления устройствами, ограничений на основе местоположения), выдачи и искупления токенов, а также свойств пользователей.

Смотрите следующий раздел, который соответствует языку, который вы используете для получения более подробной информации об обработке ошибок для вашего приложения.

## <a name="net"></a>[.NET](#tab/dotnet)

При обработке исключений .NET можно использовать `ErrorCode` сам тип исключения и участника для разграничения исключений. `ErrorCode`значения являются константами типа [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Вы также можете посмотреть на [полях MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet), и [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Если [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) брошен, попробуйте [коды ошибок аутентификации и авторизации,](reference-aadsts-error-codes.md) чтобы увидеть, если код указан там.

### <a name="common-net-exceptions"></a>Общие исключения .NET

Вот общие исключения, которые могут быть брошены и некоторые возможные смягчения:  

| Исключение | Код ошибки | Меры по снижению риска|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: Пользователь или администратор не дал согласия на использование приложения с ID 'appId' под названием 'appName'. Send an interactive authorization request for this user and resource. (Пользователь или администратор не предоставили разрешение на использование приложения с идентификатором "{идентификатор_приложения}" и именем "{имя_приложения}". Отправьте интерактивный запрос авторизации для этого пользователя и ресурса.)| Необходимо сначала получить согласие пользователя. Если вы не используете ядро .NET (у которого нет веб-мина), позвоните (только один раз). `AcquireTokeninteractive` Если вы используете ядро .NET или не `AcquireTokenInteractive`хотите делать, пользователь может перейти `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`на URL, чтобы дать согласие: . для `AcquireTokenInteractive`вызова:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: Пользователь обязан использовать многофакторную аутентификацию (MFA).| Смягчения последствий не существует. Если MFA настроен для вашего арендатора, а Azure Active Directory (AAD) принимает решение о `AcquireTokenInteractive` `AcquireTokenByDeviceCode`его применении, вам необходимо отступить к интерактивному потоку, например или .|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: Тип гранта не поддерживается в течение */общих* или */потребителей* конечных точек. Use the */organizations* or tenant-specific endpoint. You used */common*. (Тип предоставления не поддерживается через конечные точки /common или /consumers. Используйте конечную точку /organizations или конечную точку конкретного клиента. Вы использовали /common).| Как описано в сообщении от AAD, центр должен иметь клиента или конечную точку */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: Тело запроса должно содержать `client_secret or client_assertion`следующий параметр: .| Это исключение может быть брошено, если ваше приложение не было зарегистрировано в качестве публичного клиентского приложения в Azure AD. На портале Azure отредакните `allowPublicClient` манифест `true`для приложения и установите его на . |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: Не удалось идентифицировать зарегистрированный в пользователе| Библиотека не смогла задать запрос текущему пользователю входа в систему Windows, или этот пользователь не присоединился к AD или AAD (рабочие места, к он не сбитые пользователи). Смягчение 1: на UWP, убедитесь, что приложение имеет следующие возможности: Корпоративная аутентификация, частные сети (клиент и сервер), информация учетной записи пользователя. Смягчение 2: Реализация собственной логики, чтобы получить john@contoso.comимя пользователя `AcquireTokenByIntegratedWindowsAuth` (например, ) и использовать форму, которая принимает в имя пользователя.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Этот метод использует протокол, предоставляемый Active Directory (AD). Если пользователь был создан в Azure Active Directory без дублирующей записи в AD ("управляемого" пользователя), этот метод завершится ошибкой. Пользователи, созданные в AD и продублированные в AAD ("федеративные" пользователи), могут использовать такой неинтерактивный метод аутентификации. Смягчение: Используйте интерактивную аутентификацию.|

### `MsalUiRequiredException`

Один из кодов общего статуса, `MsalError.InvalidGrantError`возвращенных из MSAL.NET при вызове. `AcquireTokenSilent()` Этот код состояния означает, что приложение должно снова вызвать библиотеку аутентификации, но в интерактивном режиме (AcquireTokenInteractive или AcquireTokenByDeviceCodeFlow для общедоступных клиентских приложений, и сделать вызов в веб-приложениях). Это связано с тем, что требуется дополнительное взаимодействие с пользователем, прежде чем можно будет выдать токен проверки подлинности.

В большинстве `AcquireTokenSilent` случаев, когда сбой, это потому, что кэш маркеров не имеет маркеров, соответствующих вашему запросу. Срок действия токенов доступа `AcquireTokenSilent` истекает через 1 час, и будет пытаться получить новый на основе маркера обновления (в терминах OAuth2 это поток "Refresh Token"). Этот поток также может выходить из строя по различным причинам, например, если админ-нанимает более строгие политики входа. 

Взаимодействие направлено на то, чтобы пользователь сделал действие. Некоторые из этих условий легко решить (например, принять Условия использования одним щелчком мыши), а некоторые не могут быть решены с текущей конфигурацией (например, машина, о которой идет речь, должна подключиться к определенной корпоративной сети). Некоторые помогают пользователю настроить многофакторную аутентификацию или устанавливают на свое устройство Microsoft Authenticator.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`перечисление классификации

MSAL предоставляет `Classification` поле, которое можно прочитать, чтобы обеспечить лучший пользовательский опыт, например, чтобы сообщить пользователю, что срок действия пароля истек или что ему нужно будет дать согласие на использование некоторых ресурсов. Поддерживаемые значения являются частью `UiRequiredExceptionClassification` enum:

| Классификация    | Значение           | Рекомендуемая обработка |
|-------------------|-------------------|----------------------|
| BasicAction | Условие может быть решена путем взаимодействия с пользователем во время интерактивного потока проверки подлинности. | Позвоните AcquireTokenИнтерактивно (). |
| Дополнительноедействие | Условие может быть решена путем дополнительного исправления взаимодействия с системой, вне интерактивного потока аутентификации. | Позвоните acquireTokenInteractively () чтобы показать сообщение, объясняющие действие по исправлению положения. Приложение вызова может выбрать для сокрытия потоков, требующих additional_action если пользователь вряд ли завершит действие по исправлению положения. |
| СообщениеТолько      | Условие не может быть решена в данный момент. Запуск интерактивного потока аутентификации покажет сообщение, объясняющее состояние. | Позвоните AcquireTokenInteractively () чтобы показать сообщение, которое объясняет условие. AcquireTokenInteractively () вернет ошибку UserCanceled после того, как пользователь читает сообщение и закрывает окно. Приложение вызова может выбрать, чтобы скрыть потоки, которые приводят к message_only если пользователь вряд ли выиграет от сообщения.|
| СогласиеТребуется  | Согласие пользователя отсутствует или было аннулировано. | Позвоните AcquireTokenInteractively () для пользователя, чтобы дать согласие. |
| UserPasswordExpired | Срок действия пароля пользователя истек. | Позвоните AcquireTokenInteractively () так, что пользователь может сбросить свой пароль. |
| PromptNeverFailed| Интерактивная аутентификация была вызвана с параметром prompt'never, заставляя MSAL полагаться на файлы cookie браузера и не отображать браузер. Это не удалось. | Позвоните AcquireTokenИнтерактивно () без prompt.None |
| AcquireTokenSilentНестома | MSAL SDK не имеет достаточноинформации, чтобы получить маркер из кэша. Это может быть связано с тем, что в кэше нет токенов или не найдена учетная запись. Сообщение об ошибке имеет более подробную информацию.  | Позвоните AcquireTokenИнтерактивно (). |
| Отсутствуют    | Других подробностей не приводится. Условие может быть решено путем взаимодействия с пользователем во время интерактивного потока проверки подлинности. | Позвоните AcquireTokenИнтерактивно (). |

## <a name="net-code-example"></a>Пример кода .NET

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

MSAL.js предоставляет объекты ошибок, которые абстрагируются и классифицируют различные типы распространенных ошибок. Он также предоставляет интерфейс для доступа к конкретным сведениям об ошибках, таких как сообщения об ошибках, чтобы обрабатывать их надлежащим образом.

### <a name="error-object"></a>Объект ошибки

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

Расширяя класс Error, вы получаете доступ к следующим свойствам.
- `AuthError.message`: То `errorMessage`же самое, что и .
- `AuthError.stack`: Стек след для брошенных ошибок.

### <a name="error-types"></a>Типы ошибок

Ниже приведены доступные типы ошибок.

- `AuthError`: Базовый класс ошибок для библиотеки MSAL.js, также используемый для непредвиденных ошибок.

- `ClientAuthError`: Класс ошибки, который обозначает проблему с проверкой подлинности клиента. К типу ClientAuthErrors относятся почти все ошибки, возникающие в этой библиотеке. Эти ошибки являются результатом таких вещей, как вызов метода входа, когда логин уже выполняется, пользователь отменяет логин и так далее.

- `ClientConfigurationError`: Класс ошибок, расширяется, брошенный `ClientAuthError` до того, как запросы делаются, когда данные параметры конфигурации пользователя неформируются или отсутствуют.

- `ServerError`: Класс ошибок представляет строки ошибок, отправленные сервером проверки подлинности. Это могут быть такие ошибки, как недопустимый формат запроса или недопустимые параметры, а также любые другие ошибки, не позволяющие серверу выполнить аутентификацию или авторизацию пользователя.

- `InteractionRequiredAuthError`: Класс ошибок, распространяется `ServerError` на представление ошибок сервера, которые требуют интерактивного вызова. Эта ошибка брошена, `acquireTokenSilent` если пользователь обязан взаимодействовать с сервером, чтобы предоставить учетные данные или согласие на аутентификацию/авторизацию. Коды ошибок включают, `"interaction_required"` `"login_required"`, и `"consent_required"`.

Для обработки ошибок в потоках`loginRedirect`аутентификации с помощью методов перенаправления (, `acquireTokenRedirect`), вам `handleRedirectCallback()` нужно зарегистрировать обратный вызов, который называется с успехом или неудачей после перенаправления метода следующим образом:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Методы для всплывающих взаимодействий (`loginPopup`, `acquireTokenPopup`) возвращают обещания, поэтому вы можете использовать для их обработки шаблон обещания (.then и .catch), как показано далее:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Ошибки, требующие взаимодействия

Ошибка возвращается при попытке использовать неинтерактивный метод приобретения `acquireTokenSilent`маркера, например, но MSAL не может сделать это молча.

Возможны следующие причины:

- требуется вход в систему;
- требуется предоставить согласие;
- нужно пройти многофакторную проверку подлинности.

Чтобы избавиться от этой ошибки, нужно вызывать интерактивный метод, например `acquireTokenPopup` или `acquireTokenRedirect`:

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="python"></a>[Python](#tab/python)

В MSAL для Python большинство ошибок передаются как значение возврата от вызова API. Ошибка представлена как словарь, содержащий ответ JSON от платформы идентификации Майкрософт.

* Успешный ответ `"access_token"` содержит ключ. Формат ответа определяется протоколом OAuth2. Для получения дополнительной [информации см.](https://tools.ietf.org/html/rfc6749#section-5.1)
* Ответ на `"error"` ошибку `"error_description"`содержит и обычно . Формат ответа определяется протоколом OAuth2. Для получения дополнительной информации [см.](https://tools.ietf.org/html/rfc6749#section-5.2)

При возврате ошибки `"error_description"` ключ содержит читаемое человеком сообщение; который, в свою очередь, обычно содержит код ошибки платформы идентификации Майкрософт. Для получения подробной информации о [Authentication and authorization error codes](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes)различных кодах ошибок см.

В MSAL для Python исключения редки, поскольку большинство ошибок обрабатываются путем возврата значения ошибки. Исключение `ValueError` мгновятся только в том случае, если возникает проблема с тем, как вы пытаетесь использовать библиотеку, например, когда параметр API (ы) неработает.

## <a name="java"></a>[Java](#tab/java)

В MSAL для Java существует три типа `MsalClientException` `MsalServiceException`исключений: , и `MsalInteractionRequiredException`; все, что `MsalException`наследует от .

- `MsalClientException`выбрасывается, когда происходит ошибка, которая является локальной в библиотеке или устройстве.
- `MsalServiceException`выбрасывается, когда безопасная служба токенов (STS) возвращает ответ на ошибку или возникает другая ошибка сети.
- `MsalInteractionRequiredException`брошен, когда взаимодействие с uI требуется для успешной проверки подлинности.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException`разоблачает заголовки HTTP, возвращенные в запросах в СТС. Доступ к ним через`MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Один из общих кодов статуса, `AcquireTokenSilently()` возвращенных из MSAL для Java при вызове. `InvalidGrantError` Это означает, что требуется дополнительное взаимодействие с пользователем, прежде чем можно будет выдать токен аутентификации. Приложение должно снова позвонить в библиотеку аутентификации, но в интерактивном режиме, отправив `AuthorizationCodeParameters` или `DeviceCodeParameters` для публичных клиентских приложений.

В большинстве `AcquireTokenSilently` случаев, когда сбой, это потому, что кэш маркеров не имеет маркера, соответствующий вашему запросу. Срок действия токенов доступа `AcquireTokenSilently` истекает через час, и он будет пытаться получить новый на основе маркера обновления. С точки зрения OAuth2 это поток Refresh Token. Этот поток также может выходить из строя по различным причинам, например, когда админ-арендатор настраивает более строгие политики входа.

Некоторые условия, которые приводят к этой ошибке, легко решить пользователям. Например, им может потребоваться принять Условия использования. Или, возможно, запрос не может быть выполнен с текущей конфигурацией, потому что машина должна подключиться к определенной корпоративной сети.

MSAL предоставляет `reason` поле, которое можно использовать для лучшего пользовательского интерфейса. Например, `reason` поле может привести вас к тому, что пользователь сообщит, что срок действия пароля истек или что ему нужно будет дать согласие на использование некоторых ресурсов. Поддерживаемые значения являются частью `InteractionRequiredExceptionReason` enum:

| Причина | Значение | Рекомендуемая обработка |
|---------|-----------|-----------------------------|
| `BasicAction` | Условие может быть решено путем взаимодействия с пользователем во время интерактивного потока проверки подлинности | Звонок `acquireToken` с интерактивными параметрами |
| `AdditionalAction` | Условие может быть решена путем дополнительного исправления взаимодействия с системой за пределами интерактивного потока аутентификации. | Звоните `acquireToken` с интерактивными параметрами, чтобы показать сообщение, объясняющие действия по исправлению положения. Приложение вызова может скрыть потоки, которые требуют дополнительных действий, если пользователь вряд ли завершит исправление. |
| `MessageOnly` | Условие не может быть решена в данный момент. Запуск интерактивного потока аутентификации, чтобы показать сообщение, объясняющее состояние. | Звоните `acquireToken` с интерактивными параметрами, чтобы показать сообщение, объясняя состояние. `acquireToken`возвращает ошибку `UserCanceled` после того, как пользователь читает сообщение и закрывает окно. Приложение может выбрать, чтобы скрыть потоки, которые приводят к сообщению, если пользователь вряд ли выиграет от сообщения. |
| `ConsentRequired`| Согласие пользователя отсутствует или было аннулировано. |Звоните `acquireToken` с интерактивными параметрами, чтобы пользователь мог дать согласие. |
| `UserPasswordExpired` | Срок действия пароля пользователя истек. | Звоните `acquireToken` с интерактивным параметром, чтобы пользователь мог сбросить свой пароль |
| `None` |  Более подробная информация предоставляется. Условие может быть решено путем взаимодействия с пользователем во время интерактивного потока проверки подлинности. | Звонок `acquireToken` с интерактивными параметрами |

### <a name="code-example"></a>Пример кода

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

## <a name="iosmacos"></a>[iOS/macOS](#tab/iosmacos)

Полный список ошибок MSAL для iOS и macOS указан в [enum MSALError.](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)

Все ошибки MSAL `MSALErrorDomain` возвращаются с помощью домена.

Для системных ошибок MSAL возвращает исходизрацию `NSError` из API системы. Например, если приобретение токенов не удается из-за отсутствия `NSURLErrorDomain` подключения `NSURLErrorNotConnectedToInternet` к сети, MSAL возвращает ошибку с доменом и кодом.

Мы рекомендуем вам обрабатывать по крайней мере следующие две ошибки MSAL на стороне клиента:

- `MSALErrorInteractionRequired`: Пользователь должен сделать интерактивный запрос. Существует множество условий, которые могут привести к этой ошибке, например, с истекшим сеансом проверки подлинности или необходимостью дополнительных требований к аутентификации. Вызовите API приобретения интерактивных токенов MSAL для восстановления. 

- `MSALErrorServerDeclinedScopes`: Некоторые или все области были отклонены. Решите, продолжать ли продолжать только с предоставленными областями, или остановить процесс вхинга.

> [!NOTE]
> Enum `MSALInternalError` следует использовать только для ссылки и отладки. Не пытайтесь автоматически обрабатывать эти ошибки во время выполнения. Если ваше приложение сталкивается с какими-либо ошибками, подпадающими под `MSALInternalError`них, вы можете показать общее сообщение пользователя, объясняющее, что произошло.

Например, `MSALInternalErrorBrokerResponseNotReceived` означает, что пользователь не завершил аутентификацию и вручную вернулся в приложение. В этом случае приложение должно показать общее сообщение об ошибке, объясняющее, что проверка подлинности не завершена, и предложить, чтобы они попытались снова проверить подлинность.

Следующий пример кода Objective-C демонстрирует лучшие практики для обработки некоторых распространенных условий ошибки:

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

## <a name="conditional-access-and-claims-challenges"></a>Условный доступ и запросы утверждений

При бесшумном получении токенов приложение может получать ошибки, когда задача [требований условного доступа,](../azuread-dev/conditional-access-dev-guide.md) такая как политика MFA, требуется API, к которым вы пытаетесь получить доступ.

Шаблон для обработки этой ошибки заключается в интерактивном приобретении маркера с помощью MSAL. Интерактивное получение токена передает запрос пользователю и предоставляет ему возможность выполнить требования политики условного доступа.

В некоторых случаях при вызове API, требующих условного доступа, вы можете получать запрос утверждений прямо в полученной от API ошибке. Например, если политика условного доступа состоит в том, чтобы иметь управляемое устройство (Intune), ошибка будет чем-то вроде [AADSTS53000: Ваше устройство необходимо для того, чтобы получить доступ к этому ресурсу](reference-aadsts-error-codes.md) или что-то подобное. В этом случае вы можете передать утверждения в запросе на получение токена, чтобы пользователь получил приглашение выполнить требования политики.

### <a name="net"></a>.NET

При вызове API, который требует условного доступа через MSAL.NET, приложение должно обрабатывать исключения, связанные с запросом утверждений. Они передаются в виде [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) с непустым свойством [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet).

Чтобы справиться с проблемой претензии, `.WithClaim()` необходимо `PublicClientApplicationBuilder` использовать метод класса.

### <a name="javascript"></a>JavaScript

При бесшумном получении `acquireTokenSilent`токенов (с помощью) с помощью MSAL.js ваше приложение может получать ошибки, когда задача [требований условного доступа,](../azuread-dev/conditional-access-dev-guide.md) такая как политика MFA, требуется API, к которым вы пытаетесь получить доступ.

Для обработки этой ошибки следует выполнить интерактивный вызов для получения токена через MSAL.js, например `acquireTokenPopup` или `acquireTokenRedirect`, как в следующем примере:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    if (error instanceof InteractionRequiredAuthError) {
        // Extract claims from error message
        accessTokenRequest.claimsRequest = extractClaims(error.errorMessage);
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
    }
});
```

Интерактивное получение токена передает запрос пользователю и предоставляет ему возможность выполнить требования политики условного доступа.

При вызове API, требующих условного доступа, вы можете получать запрос утверждений прямо в полученной от API ошибке. В этом случае вы можете передать претензии, `claimsRequest` возвращенные `AuthenticationParameters.ts` по ошибке, в поле класса для удовлетворения соответствующей политики. 

Более [подробную информацию смотрите в запросе дополнительных претензий.](active-directory-optional-claims.md)

### <a name="msal-for-ios-and-macos"></a>MSAL для iOS и MacOS

MSAL для iOS и macOS позволяет запрашивать конкретные претензии как в интерактивных, так и в бесшумных сценариях приобретения токенов.

Чтобы запросить пользовательские `MSALSilentTokenParameters` `MSALInteractiveTokenParameters`претензии, укажите `claimsRequest` в или .

Для получения дополнительной информации [см.](request-custom-claims.md)

## <a name="retrying-after-errors-and-exceptions"></a>Повторные попытки после ошибок и исключений

Ожидается, что при вызове MSAL вы будете реализовывать собственные политики повторной попытки. MSAL делает HTTP вызовы службы AAD, и иногда могут возникнуть сбои, например, сеть может выйти из строя или сервер перегружен.  

### <a name="http-error-codes-500-600"></a>Коды ошибок HTTP 500–600

MSAL.NET реализует простой механизм однократного повтора для всех ошибок с кодами HTTP 500–600.

### <a name="http-429"></a>HTTP 429

Когда сервер токенов службы (STS) перегружен слишком большим количеством запросов, он возвращает ошибку HTTP 429 с подсказкой о том, как долго, пока вы не сможете попробовать еще раз в поле `Retry-After` ответа.

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) поверхностей `System.Net.Http.Headers.HttpResponseHeaders` в `namedHeaders`качестве свойства . Для повышения надежности приложений можно использовать дополнительную информацию из кода ошибки. В описанном случае можно `RetryAfterproperty` использовать (типа) `RetryConditionHeaderValue`и вычислять, когда повторить попытку.

Вот пример для приложения daemon с использованием потока учетных данных клиента. Вы можете адаптировать это к любому из методов приобретения токена.

```csharp
do
{
    retry = false;
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                           .ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (ex.ErrorCode == "temporarily_unavailable")
         {
             RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
             if (retryAfter.Delta.HasValue)
             {
                 delay = retryAfter.Delta;
             }
             else if (retryAfter.Date.HasValue)
             {
                 delay = retryAfter.Date.Value.Offset;
             }
         }
    }
    …
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```
