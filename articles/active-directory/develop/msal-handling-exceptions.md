---
title: Ошибки и исключения (MSAL) | Azure
description: Узнайте, как обрабатывать в приложениях MSAL ошибки, исключения, условный доступ и запрос утверждений.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c37a52ee939e6144b98e6a1369f94beabc5fc1d9
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532870"
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
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: The user or administrator has not consented to use the application with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource. (Пользователь или администратор не предоставили разрешение на использование приложения с идентификатором "{идентификатор_приложения}" и именем "{имя_приложения}". Отправьте интерактивный запрос авторизации для этого пользователя и ресурса.)| Необходимо сначала получить согласие пользователя. Если вы не используете .NET Core (которая не предоставляет никаких веб-интерфейсов), однократно вызовите `AcquireTokeninteractive`. Если вы используете .NET Core или не хотите выполнять `AcquireTokenInteractive`, пользователь может предоставить согласие по следующему URL-адресу: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read. Чтобы вызвать `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`.|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: The user is required to use multi-factor authentication. (Пользователь должен пройти многофакторную проверку подлинности.)| Эту ошибку устранить невозможно. Если для клиента настроена многофакторная проверка подлинности и AAD применяет ее, необходимо вернуться к интерактивному процессу входа, например `AcquireTokenInteractive` или `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: The grant type is not supported over the */common* or */consumers* endpoints. Use the */organizations* or tenant-specific endpoint. You used */common*. (Тип предоставления не поддерживается через конечные точки /common или /consumers. Используйте конечную точку /organizations или конечную точку конкретного клиента. Вы использовали /common).| Как описано в сообщении от AAD, центр должен иметь клиента или конечную точку */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'. (Текст запроса должен содержать следующий параметр: "client_secret или client_assertion".)| Такое может произойти, если приложение не зарегистрировано в качестве общедоступного клиентского приложения в AAD. Измените манифест приложения на портале Azure и задайте для параметра `allowPublicClient` значение `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user Message: Could not identify logged in user. (Не удалось идентифицировать пользователя, вошедшего в систему.)| Библиотека не может получить сведения о пользователе, вошедшем в систему Windows, или этот пользователь не присоединен к AD или AAD (не поддерживаются пользователи, присоединенные к рабочему месту). Решение 1. Проверьте на UWP, что приложение имеет следующие возможности: корпоративная аутентификация, частные сети (клиент и сервер), данные учетной записи пользователя. Решение 2. Создайте собственную логику для получения имени пользователя (например, john@contoso.com) и примените форму `AcquireTokenByIntegratedWindowsAuth`, которая принимает имя пользователя.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Этот метод использует протокол, предоставляемый Active Directory (AD). Если пользователь был создан в Azure Active Directory без дублирующей записи в AD ("управляемого" пользователя), этот метод завершится ошибкой. Пользователи, созданные в AD и продублированные в AAD ("федеративные" пользователи), могут использовать такой неинтерактивный метод аутентификации. Решение. Используйте интерактивный режим аутентификации.|

## <a name="javascript-errors"></a>Ошибки JavaScript

MSAL.js предоставляет объекты Error, которые абстрагируют и классифицируют распространенные типы ошибок и предоставляют интерфейс для получения конкретных сведений об обрабатываемых ошибках, например текстовых сообщений.

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
* **AuthError.message:** То же, что и errorMessage.
* **AuthError.stack:** Трассировка стека для возникающих ошибок. Позволяет отследить исходную точку возникновения ошибки.

**Типы ошибок**

Ниже приведены доступные типы ошибок.

* *AuthError.* Базовый класс ошибки в библиотеке MSAL.js, который также используется для непредвиденных ошибок.

* *ClientAuthError.* Класс ошибки, который обозначает проблему с аутентификацией клиента. К типу ClientAuthErrors относятся почти все ошибки, возникающие в этой библиотеке. Например, это может быть вызов метода входа во время процесса входа, отмена входа пользователем и т. д.

* *ClientConfigurationError.* Класс ошибки, расширяющий ClientAuthError, который возникает перед выполнением запросов, если отсутствуют или неправильно указаны параметры конфигурации для пользователя.

* *ServerError.* Класс ошибки для представления строк ошибок, отправленных сервером аутентификации. Это могут быть такие ошибки, как недопустимый формат запроса или недопустимые параметры, а также любые другие ошибки, не позволяющие серверу выполнить аутентификацию или авторизацию пользователя.

* *InteractionRequiredAuthError.* Класс ошибок, расширяющий ServerError, для представления ошибок сервера, которые требуют вызвать интерактивный запрос. Они создаются в `acquireTokenSilent`, если пользователю необходимо связаться с сервером и предоставить учетные данные или согласие для целей аутентификации и (или) авторизации. Примеры кодов ошибок: interaction_required, login_required, consent_required.

Для обработки ошибок в потоках аутентификации с методами перенаправления (`loginRedirect`, `acquireTokenRedirect`) следует зарегистрировать после перенаправления обратный вызов, который будет вызываться при успехе или неудаче, используя метод `handleRedirectCallback()` следующим образом:

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

### <a name="interaction-required-errors"></a>Ошибки из-за необходимости взаимодействия

Такая ошибка возвращается, если требуется взаимодействие с пользовательским интерфейсом. Это означает, что вы попытались применить неинтерактивный метод получения токена (например, `acquireTokenSilent`), который MSAL не может выполнить автоматически. Возможны следующие причины:

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
    // call acquireTokenPopup in case of acquireTokenSilent failure
    myMSALObj.acquireTokenPopup(accessTokenRequest).then(
        function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
});
```

Интерактивное получение токена передает запрос пользователю и предоставляет ему возможность выполнить требования политики условного доступа.

При вызове API, требующих условного доступа, вы можете получать запрос утверждений прямо в полученной от API ошибке. В этом случае вы можете передать утверждения, полученные в `extraQueryParameters` этой ошибки, в запросе на получение токена, чтобы пользователь получил приглашение выполнить требования политики.

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>Повторные попытки после ошибок и исключений

### <a name="http-error-codes-500-600"></a>Коды ошибок HTTP 500–600
MSAL.NET реализует простой механизм однократного повтора для всех ошибок с кодами HTTP 500–600.

### <a name="http-429"></a>HTTP 429
Если сервер токенов службы слишком занят, то есть получает "слишком много запросов", он возвращает ошибку с кодом HTTP 429 и подсказкой о рекомендуемом времени следующей попытки (поле ответа Retry-After) в формате задержки в секундах или даты.

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
