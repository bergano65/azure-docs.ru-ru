---
title: Ошибки и исключения (MSAL) | Azure
description: Узнайте, как обрабатывать в приложениях MSAL ошибки, исключения, условный доступ и запрос утверждений.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/19/2019
ms.author: negoe
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: fe3ad29cfd113deba5824ce25721dc543c6267c0
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305048"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>Обработка исключений и ошибок с помощью MSAL
В библиотеке аутентификации Майкрософт (MSAL) применяются исключения, которые позволяют разработчикам приложений устранять проблемы самостоятельно, не отображая сообщения об ошибках пользователям. Сообщения об исключениях не локализованы.

При обработке ошибок и исключений вы можете различать их по типам исключений и кодам ошибки.  Список кодов ошибок аутентификации и авторизации можно найти [здесь](reference-aadsts-error-codes.md).

## <a name="net-exceptions"></a>Исключения .NET
При обработке исключений вы можете различать их по типам исключений и элементу `ErrorCode`. Значения `ErrorCode` являются константами типа [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Вы можете также проверить значения полей [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) и [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Если возникает прерывание [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet), код ошибки кода можно найти в [списке кодов ошибок при аутентификации и авторизации](reference-aadsts-error-codes.md).

### <a name="common-exceptions"></a>Распространенные исключения
Ниже перечислены несколько исключений, которые встречаются чаще других, и предложения по их устранению.

| Исключение | Код ошибки | Предотвращение|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: The user or administrator has not consented to use the application with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource. (Пользователь или администратор не предоставили разрешение на использование приложения с идентификатором "{идентификатор_приложения}" и именем "{имя_приложения}". Отправьте интерактивный запрос авторизации для этого пользователя и ресурса.)| Необходимо сначала получить согласие пользователя. Если вы не используете .NET Core (которая не предоставляет никаких веб-интерфейсов), однократно вызовите `AcquireTokeninteractive`. Если вы используете .NET Core или не хотите делать это `AcquireTokenInteractive`, пользователь может переходить по URL-адресу, чтобы предоставить согласие:. https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read Чтобы вызвать `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`.|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: The user is required to use multi-factor authentication. (Пользователь должен пройти многофакторную проверку подлинности.)| Эту ошибку устранить невозможно. Если для клиента настроена многофакторная проверка подлинности и AAD применяет ее, необходимо вернуться к интерактивному процессу входа, например `AcquireTokenInteractive` или `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: The grant type is not supported over the */common* or */consumers* endpoints. Use the */organizations* or tenant-specific endpoint. You used */common*. (Тип предоставления не поддерживается через конечные точки /common или /consumers. Используйте конечную точку /organizations или конечную точку конкретного клиента. Вы использовали /common).| Как описано в сообщении от AAD, центр должен иметь клиента или конечную точку */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'. (Текст запроса должен содержать следующий параметр: "client_secret или client_assertion".)| Это исключение может произойти, если приложение не было зарегистрировано как общедоступное клиентское приложение в Azure AD. Измените манифест приложения на портале Azure и задайте для параметра `allowPublicClient` значение `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user Message: Could not identify logged in user. (Не удалось идентифицировать пользователя, вошедшего в систему.)| Библиотека не может получить сведения о пользователе, вошедшем в систему Windows, или этот пользователь не присоединен к AD или AAD (не поддерживаются пользователи, присоединенные к рабочему месту). Решение 1. Проверьте на UWP, что приложение имеет следующие возможности: корпоративная аутентификация, частные сети (клиент и сервер), данные учетной записи пользователя. Решение 2. Создайте собственную логику для получения имени пользователя (например, john@contoso.com) и примените форму `AcquireTokenByIntegratedWindowsAuth`, которая принимает имя пользователя.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Этот метод использует протокол, предоставляемый Active Directory (AD). Если пользователь был создан в Azure Active Directory без дублирующей записи в AD ("управляемого" пользователя), этот метод завершится ошибкой. Пользователи, созданные в AD и продублированные в AAD ("федеративные" пользователи), могут использовать такой неинтерактивный метод аутентификации. Решение. Используйте интерактивный режим аутентификации.|

## <a name="javascript-errors"></a>Ошибки JavaScript

MSAL. js предоставляет объекты ошибок, которые являются абстрактными и классифицируют различные типы распространенных ошибок. Он также предоставляет интерфейс для доступа к конкретным сведениям об ошибках, таким как сообщения об ошибке для их правильной работы.

**Объект Error**

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
* **AuthError.message:**  То же, что и errorMessage.
* **AuthError.stack:** Трассировка стека для возникающих ошибок. Позволяет отследить исходную точку возникновения ошибки.

**Типы ошибок**

Ниже приведены доступные типы ошибок.

* *AuthError.* Базовый класс ошибки в библиотеке MSAL.js, который также используется для непредвиденных ошибок.

* *ClientAuthError.* Класс Error, который обозначает проблемы с проверкой подлинности клиента. К типу ClientAuthErrors относятся почти все ошибки, возникающие в этой библиотеке. Эти ошибки, например вызов метода входа при выполнении входа, пользователи отменяют вход и т. д. 

* *ClientConfigurationError.* Класс Error расширяет Клиентаусеррор, вызываемый перед выполнением запросов, если параметры пользовательской конфигурации имеют неправильный формат или отсутствуют.

* *ServerError.* Класс ошибки для представления строк ошибок, отправленных сервером аутентификации. Это могут быть такие ошибки, как недопустимый формат запроса или недопустимые параметры, а также любые другие ошибки, не позволяющие серверу выполнить аутентификацию или авторизацию пользователя.

* *InteractionRequiredAuthError.* Класс Error, расширяющий ServerError для представления ошибок сервера, требующих интерактивного вызова. Эта ошибка возникает, `acquireTokenSilent` если пользователю необходимо взаимодействовать с сервером для предоставления учетных данных или согласия на проверку подлинности или авторизацию. Примеры кодов ошибок: interaction_required, login_required, consent_required.

Для обработки ошибок в потоках проверки подлинности`loginRedirect`с `acquireTokenRedirect`помощью методов перенаправления (,) необходимо зарегистрировать обратный вызов, который вызывается с успешным или неудачным `handleRedirectCallback()` после перенаправления с помощью метода следующим образом:

```javascript
function authCallback(error, response) {
    //handle redirect response
}


var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);

myMSALObj.acquireTokenRedirect(request);
```

Методы для всплывающих окон (`loginPopup`, `acquireTokenPopup`) возвращают обещания, поэтому для их работы можно использовать шаблон Promise (. then и. catch), как показано ниже.

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Ошибки из-за необходимости взаимодействия

Если при попытке использовать Неинтерактивный метод получения маркера (например, `acquireTokenSilent`) и MSAL не смог выполнить его автоматически, возвращается ошибка. 

Возможны следующие причины:

* требуется вход в систему;
* требуется предоставить согласие;
* нужно пройти многофакторную проверку подлинности.

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

## <a name="conditional-access-and-claims-challenges"></a>Условный доступ и запросы утверждений
Если вы получаете токены автоматически, приложение может получать ошибки, когда для используемого API применяются [запросы утверждений условного доступа](conditional-access-dev-guide.md), например политики многофакторной проверки подлинности.

Для обработки этой ошибки следует получить токен в интерактивном режиме с помощью MSAL. Интерактивное получение токена передает запрос пользователю и предоставляет ему возможность выполнить требования политики условного доступа.

В некоторых случаях при вызове API, требующих условного доступа, вы можете получать запрос утверждений прямо в полученной от API ошибке. Например, если политика условного доступа требует использовать управляемое устройство (Intune), вы получите ошибку [AADSTS53000: Your device is required to be managed to access this resource](reference-aadsts-error-codes.md) (Требуется управляемое устройство для доступа к этому ресурсу) или что-то подобное. В этом случае вы можете передать утверждения в запросе на получение токена, чтобы пользователь получил приглашение выполнить требования политики.

### <a name="net"></a>.NET
При вызове API, который требует условного доступа через MSAL.NET, приложение должно обрабатывать исключения, связанные с запросом утверждений. Они передаются в виде [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) с непустым свойством [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet).

Чтобы обработать такой запрос утверждений, следует использовать метод `.WithClaim()` из класса `PublicClientApplicationBuilder`.

### <a name="javascript"></a>JavaScript
Если вы получаете токены автоматически (с помощью `acquireTokenSilent`) через MSAL.js, приложение может получать ошибки, если для используемого API применяются [запросы утверждений условного доступа](conditional-access-dev-guide.md), например политики многофакторной проверки подлинности.

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

При вызове API, требующих условного доступа, вы можете получать запрос утверждений прямо в полученной от API ошибке. В этом случае можно передать утверждения, возвращенные в сообщении об ошибке `claimsRequest` , в поле `AuthenticationParameters.ts` класса, чтобы удовлетворить соответствующую политику. 

Дополнительные сведения см. в статье [запрос дополнительных утверждений](active-directory-optional-claims.md) .

## <a name="retrying-after-errors-and-exceptions"></a>Повторные попытки после ошибок и исключений

### <a name="http-error-codes-500-600"></a>Коды ошибок HTTP 500–600
MSAL.NET реализует простой механизм однократного повтора для всех ошибок с кодами HTTP 500–600.

### <a name="http-429"></a>HTTP 429
Когда сервер маркеров службы (STS) перегружается с слишком большим количеством запросов, он возвращает ошибку HTTP 429 с указанием о том, когда можно повторить попытку через некоторое время. Ошибку можно считать из `Retry-After` поля ответа.

#### <a name="net"></a>.NET
Исключение [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) предоставляет `System.Net.Http.Headers.HttpResponseHeaders` как свойство `namedHeaders`. Таким образом, вы можете использовать дополнительные сведения из кода ошибки для повышения надежности приложений. В описанном выше случае можно использовать `RetryAfterproperty` (с типом `RetryConditionHeaderValue`) для вычисления времени повторной попытки.

Мы предлагаем пример приложения для управляющей программы (с клиентским потоком учетных данных), но вы можете адаптировать его и использовать любой метод получения токена.

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
