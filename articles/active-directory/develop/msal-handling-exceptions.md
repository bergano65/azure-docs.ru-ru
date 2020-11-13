---
title: Ошибки и исключения (MSAL)
titleSuffix: Microsoft identity platform
description: Узнайте, как обрабатывать в приложениях MSAL ошибки, исключения, условный доступ и запрос утверждений.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 02a08cc0400b4d65577c13282ca4c23cac1d21dc
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578932"
---
# <a name="handle-msal-exceptions-and-errors"></a>Обработка ошибок и исключений MSAL

В этой статье приводятся общие сведения о различных типах ошибок и рекомендации по обработке распространенных ошибок входа.

## <a name="msal-error-handling-basics"></a>Основные сведения об обработке ошибок MSAL

В библиотеке проверки подлинности Майкрософт (MSAL) применяются исключения, которые позволяют разработчикам приложений устранять проблемы самостоятельно, не отображая сообщения об ошибках пользователям. Сообщения об исключениях не локализованы.

При обработке ошибок и исключений вы можете различать их по типам исключений и кодам ошибки.  Список кодов ошибок аутентификации и авторизации можно найти [здесь](reference-aadsts-error-codes.md).

Во время входа в систему могут происходить ошибки, связанные с согласованностью, условным доступом (MFA, управлением устройствами, ограничениями на основе расположения), выдачей и возвратом токенов и свойствами пользователей.

Дополнительные сведения об обработке ошибок для приложения см. в следующем разделе для того языка, который вы используете.

## <a name="net"></a>[.NET](#tab/dotnet)

При обработке исключений .NET вы можете различать их по типу и элементу `ErrorCode`. Значения `ErrorCode` являются константами типа [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Вы можете также проверить значения полей [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) и [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Если выдается исключение [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet), попробуйте проверить, нет ли в списке [кодов ошибок проверки подлинности и авторизации](reference-aadsts-error-codes.md).

### <a name="common-net-exceptions"></a>Распространенные исключения .NET

Ниже перечислены несколько исключений, которые встречаются чаще других, и предложения по их устранению.  

| Исключение | Код ошибки | Меры по снижению риска|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: The user or administrator has not consented to use the application with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource. (Пользователь или администратор не предоставили разрешение на использование приложения с идентификатором "{идентификатор_приложения}" и именем "{имя_приложения}". Отправьте интерактивный запрос авторизации для этого пользователя и ресурса.)| Необходимо сначала получить согласие пользователя. Если вы не используете платформу .NET Core (которая не предоставляет никаких веб-интерфейсов), однократно вызовите `AcquireTokeninteractive`. Если вы используете .NET Core или не хотите выполнять `AcquireTokenInteractive`, пользователь может предоставить согласие по следующему URL-адресу: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`, чтобы вызвать `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`.|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: The user is required to use [multi-factor authentication (MFA)](../authentication/concept-mfa-howitworks.md).| There is no mitigation. If MFA is configured for your tenant and Azure Active Directory (AAD) decides to enforce it, you need to fall back to an interactive flow such as `AcquireTokenInteractive` or `AcquireTokenByDeviceCode` (Пользователь должен пройти многофакторную проверку подлинности (MFA). Возможности для устранения рисков отсутствуют. Если для клиента настроена многофакторная проверка подлинности и Azure Active Directory (AAD) применяет ее, необходимо вернуться к интерактивному процессу входа, например AcquireTokenInteractive или AcquireTokenByDeviceCode).|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: The grant type isn't supported over the */common* or */consumers* endpoints. Use the */organizations* or tenant-specific endpoint. You used */common*. (Тип предоставления не поддерживается через конечные точки /common или /consumers. Используйте конечную точку /organizations или конечную точку конкретного клиента. Вы использовали /common).| Как описано в сообщении от AAD, центр должен иметь клиента или конечную точку */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: The request body must contain the following parameter: `client_secret or client_assertion` (Текст запроса должен содержать следующий параметр: client_secret или client_assertion).| Такое исключение может произойти, если приложение не зарегистрировано в качестве общедоступного клиентского приложения в Azure AD. Измените манифест приложения на портале Azure и задайте для параметра `allowPublicClient` значение `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: Could not identify logged in user. (Не удалось идентифицировать пользователя, вошедшего в систему.)| Библиотека не может получить сведения о пользователе, вошедшем в систему Windows, или этот пользователь не присоединен к AD или AAD (не поддерживаются пользователи, присоединенные к рабочему месту). Решение 1. Проверьте на UWP, что приложение имеет следующие возможности: корпоративная аутентификация, частные сети (клиент и сервер), данные учетной записи пользователя. Решение 2. Создайте собственную логику для получения имени пользователя (например, john@contoso.com) и примените форму `AcquireTokenByIntegratedWindowsAuth`, которая принимает имя пользователя.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Этот метод использует протокол, предоставляемый Active Directory (AD). Если пользователь был создан в Azure Active Directory без дублирующей записи в AD ("управляемого" пользователя), этот метод завершится ошибкой. Пользователи, созданные в AD и продублированные в AAD ("федеративные" пользователи), могут использовать такой неинтерактивный метод аутентификации. Устранение рисков. Используйте интерактивный режим аутентификации.|

### `MsalUiRequiredException`

Один из распространенных кодов состояния, возвращаемых из MSAL.NET при вызове `AcquireTokenSilent()`, — `MsalError.InvalidGrantError`. Этот код состояния означает, что приложение должно повторно вызывать библиотеку проверки подлинности, но в интерактивном режиме (использовать AcquireTokenInteractive или AcquireTokenByDeviceCodeFlow для общедоступных клиентских приложений и выполнить запрос в веб-приложениях). Это связано с тем, что перед выдачей токена проверки подлинности требуется дополнительное взаимодействие с пользователем.

В большинстве случаев происходит сбой `AcquireTokenSilent`, так как кэш токенов не содержит токены, соответствующие запросу. Срок действия маркеров доступа истекает через 1 час, а `AcquireTokenSilent` попытается получить новый на основе токена обновления (в терминах OAuth2 это поток "Обновление токена"). Этот поток также может завершиться сбоем по различным причинам, например, если администратор клиента настроит более строгие политики входа в систему. 

Цель взаимодействия — обеспечить выполнение действия пользователем. С некоторыми из этих ситуаций пользователи могут легко справиться (например, принять условия использования одним щелчком), в то время как в других решить проблему с текущей конфигурацией невозможно (например, если компьютер должен подключаться к определенной корпоративной сети). При определенных обстоятельствах пользователю может быть оказана помощь в настройке многофакторной проверки подлинности или установке Microsoft Authenticator на устройстве.

### <a name="msaluirequiredexception-classification-enumeration"></a>Перечисление классификации `MsalUiRequiredException`

MSAL предоставляет поле `Classification`, считывание которого позволяет улучшить взаимодействие с пользователем, например сообщить ему о том, что срок действия пароля истек или что ему нужно предоставить согласие на использование некоторых ресурсов. Поддерживаемые значения являются частью перечисления `UiRequiredExceptionClassification`.

| Классификация    | Значение           | Рекомендуемая обработка |
|-------------------|-------------------|----------------------|
| BasicAction | Ситуацию можно разрешить путем взаимодействия с пользователем во время интерактивного потока проверки подлинности. | Вызов AcquireTokenInteractively(). |
| AdditionalAction | Ситуацию можно разрешить с помощью дополнительного корректирующего взаимодействия с системой за пределами интерактивного потока проверки подлинности. | Вызов AcquireTokenInteractively() для отображения сообщения с объяснением корректирующего действия. Вызывающее приложение может скрыть потоки, требующие additional_action, если пользователь, скорее всего, не станет выполнять корректирующее действие. |
| MessageOnly      | В данный момент разрешить ситуацию невозможно. При запуске интерактивного потока проверки подлинности отобразится сообщение с объяснением сложившейся ситуации. | Вызов AcquireTokenInteractively() для отображения сообщения с объяснением сложившейся ситуации. AcquireTokenInteractively() возвращает ошибку UserCanceled после того, как пользователь прочтет сообщение и закроет окно. Вызывающее приложение может скрыть потоки, которые приводят к message_only, если сообщение, скорее всего, будет бесполезным для пользователя.|
| ConsentRequired  | Согласие пользователя отсутствует или отменено. | Вызовите AcquireTokenInteractively() для получения согласия пользователя. |
| UserPasswordExpired | Срок действия пароля пользователя истек. | Вызов AcquireTokenInteractively() для предоставления пользователю возможности сброса пароля. |
| PromptNeverFailed| Интерактивная проверка подлинности вызвана с параметром prompt=never, что вынудило MSAL использовать файлы cookie браузера, а не отображать запрос в браузере. Сделать это не удалось. | Вызов AcquireTokenInteractively() без Prompt.None |
| AcquireTokenSilentFailed | Пакет SDK для MSAL не содержит достаточно сведений для получения токена из кэша. Это может быть вызвано тем, что в кэше отсутствуют токены или учетная запись не найдена. Дополнительные сведения приводятся в сообщении об ошибке.  | Вызов AcquireTokenInteractively(). |
| None    | Дополнительные сведения не предоставляются. Ситуацию можно разрешить путем взаимодействия с пользователем во время интерактивного потока проверки подлинности. | Вызов AcquireTokenInteractively(). |

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

MSAL.js предоставляет объекты ошибок, которые являются абстрактными и используются для классификации различных типов распространенных ошибок. Эта библиотека также предоставляет интерфейс для доступа к конкретным сведениям об ошибках, например сообщениям об ошибках, для их правильной обработки.

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
- `AuthError.message`:  эквивалентно `errorMessage`.
- `AuthError.stack`: Трассировка стека для возникающих ошибок.

### <a name="error-types"></a>Типы ошибок

Ниже приведены доступные типы ошибок.

- `AuthError`: Базовый класс ошибки в библиотеке MSAL.js, который также используется для непредвиденных ошибок.

- `ClientAuthError`: класс ошибок, который обозначает проблему с проверкой подлинности клиента. К типу ClientAuthErrors относятся почти все ошибки, возникающие в этой библиотеке. Эти ошибки происходят из-за вызова метода входа, когда вход уже выполняется, отмены входа пользователем и т. п.

- `ClientConfigurationError`: класс ошибок, расширяющий `ClientAuthError`, который возникает перед выполнением запросов, если отсутствуют или неправильно указаны параметры конфигурации для пользователя.

- `ServerError`: класс ошибок для представления строк ошибок, отправленных сервером проверки подлинности. Это могут быть такие ошибки, как недопустимый формат запроса или недопустимые параметры, а также любые другие ошибки, не позволяющие серверу выполнить аутентификацию или авторизацию пользователя.

- `InteractionRequiredAuthError`: класс ошибок, расширяющий `ServerError`, для представления ошибок сервера, которые требуют вызвать интерактивный запрос. Такую ошибку вызывает `acquireTokenSilent`, если пользователю необходимо связаться с сервером и предоставить учетные данные или согласие для целей проверки подлинности и (или) авторизации. Коды ошибок: `"interaction_required"`, `"login_required"` и `"consent_required"`.

Для обработки ошибок в потоках проверки подлинности с методами перенаправления (`loginRedirect`, `acquireTokenRedirect`) следует зарегистрировать после перенаправления обратный вызов, который будет вызываться при успехе или неудаче, используя метод `handleRedirectCallback()` следующим образом.

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

Ошибка возвращается при попытке использовать неинтерактивный метод получения токена, например `acquireTokenSilent`, если MSAL не может получить его автоматически.

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

В MSAL для Python большинство ошибок передаются как возвращаемое значение из вызова API. Эта ошибка представлена в виде словаря, содержащего ответ JSON от платформы Microsoft Identity.

* В случае успешного выполнения ответ будет содержать ключ `"access_token"`. Формат ответа определяется протоколом OAuth2. Дополнительные сведения см. в разделе 5.1 [Успешный ответ](https://tools.ietf.org/html/rfc6749#section-5.1).
* Ответ со сведениями об ошибке содержит `"error"` и обычно `"error_description"`. Формат ответа определяется протоколом OAuth2. Дополнительные сведения см. в разделе 5.2 [Ответ со сведениями об ошибке](https://tools.ietf.org/html/rfc6749#section-5.2).

При возвращении ошибки ключ `"error_description"` содержит удобное для чтения сообщение, которое, в свою очередь, обычно содержит код ошибки платформы удостоверений Майкрософт. Дополнительные сведения см. в статье [Коды ошибок аутентификация Azure AD и авторизации](./reference-aadsts-error-codes.md).

В MSAL для Python исключения редки, поскольку большинство ошибок обрабатывается путем возврата значения ошибки. Исключение `ValueError` происходит только в случае проблем из-за попытки использовать библиотеку определенным способом, например, если параметры API имеют неправильный формат.

## <a name="java"></a>[Java](#tab/java)

В MSAL для Java существуют три типа исключений: `MsalClientException`, `MsalServiceException` и `MsalInteractionRequiredException`. Все они наследуются от `MsalException`.

- `MsalClientException` происходит в случае ошибки, которая является локальной для библиотеки или устройства.
- `MsalServiceException` происходит, когда служба токенов безопасности (STS) возвращает ответ со сведениями об ошибке или происходит другая ошибка сети.
- `MsalInteractionRequiredException` происходит, когда для прохождения проверки подлинности требуется взаимодействие с пользовательским интерфейсом.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` предоставляет HTTP-заголовки, возвращаемые в запросах к STS. Для доступа к ним используется `MsalServiceException.headers()`.

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Один из распространенных кодов состояния, возвращаемых из MSAL для Java при вызове `AcquireTokenSilently()`, — `InvalidGrantError`. Он означает, что перед выдачей токена проверки подлинности требуется дополнительное взаимодействие с пользователем. Приложение должно снова вызвать библиотеку проверки подлинности, но в интерактивном режиме, отправив `AuthorizationCodeParameters` или `DeviceCodeParameters` для общедоступных клиентских приложений.

В большинстве случаев сбой `AcquireTokenSilently` происходит из-за того, что кэш токенов не содержит токены, соответствующие запросу. Срок действия маркеров доступа истекает через один час, а `AcquireTokenSilently` попытается получить новый на основе токена обновления. В терминах OAuth2 это поток "Обновление токена". Этот поток также может завершиться сбоем по различным причинам, например, если администратор клиента настроит более строгие политики входа в систему.

С некоторыми из ситуаций, которые приводят к такой ошибке, пользователи могут легко справиться. Например, им, возможно, потребуется принять условия использования. Возможно также, что запрос не может быть выполнен с текущей конфигурацией, так как компьютер должен быть подключен к определенной корпоративной сети.

MSAL предоставляет поле `reason`, которое можно использовать для повышения удобства работы пользователей. Например, с помощью поля `reason` можно сообщить пользователю о том, что срок действия пароля истек или что ему необходимо дать согласие на использование некоторых ресурсов. Поддерживаемые значения являются частью перечисления `InteractionRequiredExceptionReason`.

| Причина | Значение | Рекомендуемая обработка |
|---------|-----------|-----------------------------|
| `BasicAction` | Ситуацию можно разрешить путем взаимодействия с пользователем во время интерактивного потока проверки подлинности. | Вызов `acquireToken` с интерактивными параметрами. |
| `AdditionalAction` | Ситуацию можно разрешить с помощью дополнительного корректирующего взаимодействия с системой за пределами интерактивного потока проверки подлинности. | Вызов `acquireToken` с интерактивными параметрами для отображения сообщения с объяснением корректирующего действия, которое необходимо выполнить. Вызывающее приложение может скрыть потоки, требующие additional_action, если пользователь, скорее всего, не станет выполнять корректирующее действие. |
| `MessageOnly` | В данный момент разрешить ситуацию невозможно. При запуске интерактивного потока проверки подлинности отобразится сообщение с объяснением сложившейся ситуации. | Вызов `acquireToken` с интерактивными параметрами для отображения сообщения с объяснением сложившейся ситуации. `acquireToken` будет возвращать ошибку `UserCanceled` после того, как пользователь прочтет сообщение и закроет окно. Это приложение может скрыть потоки, которые приводят к отображению сообщения, если оно, скорее всего, будет бесполезным для пользователя. |
| `ConsentRequired`| Согласие пользователя отсутствует или отменено. |Вызов `acquireToken` с интерактивными параметрами для получения согласия от пользователя. |
| `UserPasswordExpired` | Срок действия пароля пользователя истек. | Вызов `acquireToken` с интерактивным параметром для предоставления пользователю возможности сбросить свой пароль. |
| `None` |  Предоставляются дополнительные сведения. Ситуацию можно разрешить путем взаимодействия с пользователем во время интерактивного потока проверки подлинности. | Вызов `acquireToken` с интерактивными параметрами. |

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

## <a name="iosmacos"></a>[iOS и macOS](#tab/iosmacos)

Полный список MSAL для ошибок iOS и macOS приведен в [перечислении MSALError](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Все ошибки, вызванные MSAL, возвращаются с доменом `MSALErrorDomain`.

Для системных ошибок MSAL возвращает исходное значение `NSError` из системного интерфейса API. Например, если получение токена завершается сбоем из-за отсутствия сетевого подключения, MSAL возвращает ошибку с доменом `NSURLErrorDomain` и кодом `NSURLErrorNotConnectedToInternet`.

На стороне клиента рекомендуется выполнять обработку по крайней мере двух следующих ошибок MSAL.

- `MSALErrorInteractionRequired`: пользователь должен выполнить интерактивный запрос. Причин такой ошибки может быть много, например истечение срока действия сеанса проверки подлинности или необходимость выполнить дополнительные требования для ее проведения. Для исправления ошибки вызовите интерактивный API получения токена MSAL. 

- `MSALErrorServerDeclinedScopes`: доступ для некоторых или всех областей отклонен. Решите, следует ли использовать только области, для которых предоставлен доступ, или же остановить процесс входа.

> [!NOTE]
> Перечисление `MSALInternalError` следует использовать только для справки и отладки. Не пытайтесь автоматически обрабатывать эти ошибки во время выполнения. Если в приложении происходят какие-либо ошибки из-за перечисления `MSALInternalError`, возможно, потребуется отобразить для пользователя общее сообщение, объясняющее, что произошло.

Например, `MSALInternalErrorBrokerResponseNotReceived` означает, что пользователь не прошел проверку подлинности и вручную возвращался в приложение. В этом случае приложение должно показывать общее сообщение об ошибке, в котором объясняется, что проверка подлинности не пройдена, и предлагается ее повторить.

В следующем примере кода Objective-C демонстрируются рекомендации по обработке некоторых распространенных условий, вызывающих ошибки.

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

Если вы получаете токены автоматически, приложение может получать ошибки, когда для используемого API применяются [запросы утверждений условного доступа](../azuread-dev/conditional-access-dev-guide.md), например политики MFA.

Для обработки этой ошибки следует получить токен в интерактивном режиме с помощью MSAL. Интерактивное получение токена передает запрос пользователю и предоставляет ему возможность выполнить требования политики условного доступа.

В некоторых случаях при вызове API, требующих условного доступа, вы можете получать запрос утверждений прямо в полученной от API ошибке. Например, если политика условного доступа требует использовать управляемое устройство (Intune), вы получите ошибку [AADSTS53000: Your device is required to be managed to access this resource](reference-aadsts-error-codes.md) (Требуется управляемое устройство для доступа к этому ресурсу) или что-то подобное. В этом случае вы можете передать утверждения в запросе на получение токена, чтобы пользователь получил приглашение выполнить требования политики.

### <a name="net"></a>.NET

При вызове API, который требует условного доступа через MSAL.NET, приложение должно обрабатывать исключения, связанные с запросом утверждений. Они передаются в виде [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) с непустым свойством [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet).

Чтобы обработать такой запрос утверждений, следует использовать метод `.WithClaim()` из класса `PublicClientApplicationBuilder`.

### <a name="javascript"></a>JavaScript

Если вы получаете токены автоматически (с помощью `acquireTokenSilent`) через MSAL.js, приложение может получать ошибки, если для используемого API применяются [запросы утверждений условного доступа](../azuread-dev/conditional-access-dev-guide.md), например политики MFA.

Для обработки этой ошибки следует выполнить интерактивный вызов для получения токена через MSAL.js, например `acquireTokenPopup` или `acquireTokenRedirect`, как в следующем примере:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

Интерактивное получение токена передает запрос пользователю и предоставляет ему возможность выполнить требования политики условного доступа.

При вызове API, требующих условного доступа, вы можете получать запрос утверждений прямо в полученной от API ошибке. В этом случае можно передать утверждения, возвращенные в сообщении об ошибке, в поле `claimsRequest` класса `AuthenticationParameters.ts`, чтобы выполнить условия соответствующей политики. 

Дополнительные сведения см. в статье о [запросе дополнительных утверждений](active-directory-optional-claims.md).

### <a name="msal-for-ios-and-macos"></a>MSAL для iOS и MacOS

MSAL для iOS и macOS позволяет запрашивать определенные утверждения как в интерактивном, так и в автоматическом сценарии получения токенов.

Чтобы запросить пользовательские утверждения, укажите `claimsRequest` в `MSALSilentTokenParameters` или `MSALInteractiveTokenParameters`.

Дополнительные сведения см. в статье [Запрос пользовательских утверждений с помощью MSAL для iOS и macOS](request-custom-claims.md).

## <a name="retrying-after-errors-and-exceptions"></a>Повторные попытки после ошибок и исключений

При вызове MSAL следует реализовать собственные политики повтора попытки. MSAL выполняет HTTP-вызовы службы AAD, однако иногда при этом могут возникать сбои, например в случае сбоя сети или перегрузки сервера.  

### <a name="http-error-codes-500-600"></a>Коды ошибок HTTP 500–600

MSAL.NET реализует простой механизм однократного повтора для всех ошибок с кодами HTTP 500–600.

### <a name="http-429"></a>HTTP 429

Когда сервер токенов службы (STS) перегружен из-за слишком большого количества запросов, он возвращает ошибку HTTP 429 с указанием времени ожидания до следующей попытки в поле ответа `Retry-After`.

### <a name="net"></a>.NET

Исключение [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) предоставляет `System.Net.Http.Headers.HttpResponseHeaders` в качестве свойства `namedHeaders`. Вы можете использовать дополнительные сведения из кода ошибки для повышения надежности приложений. В описанном выше случае можно использовать `RetryAfterproperty` (с типом `RetryConditionHeaderValue`) для вычисления времени повторной попытки.

Ниже приведен пример для управляющего приложения, использующего поток учетных данных клиента. Его можно адаптировать к любому из методов получения токена.

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
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
