---
title: Ошибки и исключения (MSAL)
titleSuffix: Microsoft identity platform
description: Сведения об обработке ошибок и исключений, условном доступе и проблемах с утверждениями в MSAL приложениях.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/22/2019
ms.author: twhitney
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7f903ca541582dfa0f3980bb65a3fef3c4b774a7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916780"
---
# <a name="handle-msal-exceptions-and-errors"></a>Обработку исключений и ошибок MSAL

В этой статье приводятся общие сведения о различных типах ошибок и рекомендации по обработке общих ошибок входа.

## <a name="msal-error-handling-basics"></a>Основные сведения об обработке ошибок MSAL

Исключения в библиотеке проверки подлинности (Майкрософт) (MSAL) предназначены для разработчиков приложений для устранения неполадок — не для отображения конечным пользователям. Сообщения об исключениях не локализованы.

При обработке ошибок и исключений вы можете различать их по типам исключений и кодам ошибки.  Список кодов ошибок аутентификации и авторизации можно найти [здесь](reference-aadsts-error-codes.md).

Во время входа в систему могут возникать ошибки, связанные с согласованностью, условным доступом (MFA, управлением устройствами, ограничениями на основе расположения), выдачей и возвратом токенов и свойствами пользователя.

Дополнительные сведения об обработке ошибок для приложения см. в следующем разделе, который соответствует используемому языку.

## <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

При обработке исключений .NET можно использовать сам тип исключения и элемент `ErrorCode` для различения исключений. `ErrorCode` значения являются константами типа [мсалеррор](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Вы также можете взглянуть на поля [мсалклиентексцептион](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [мсалсервицеексцептион](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)и [мсалуирекуиредексцептион](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Если создается [мсалсервицеексцептион](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) , попробуйте проверить [подлинность и коды ошибок авторизации](reference-aadsts-error-codes.md) , чтобы узнать, присутствует ли там код.

### <a name="common-net-exceptions"></a>Распространенные исключения .NET

Ниже приведены распространенные исключения, которые могут быть вызваны, и некоторые возможные способы их устранения.  

| Исключение | Код ошибки | Устранение|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: пользователь или администратор не сослался использовать приложение с ИДЕНТИФИКАТОРом "{appId}" с именем "{appName}". Send an interactive authorization request for this user and resource. (Пользователь или администратор не предоставили разрешение на использование приложения с идентификатором "{идентификатор_приложения}" и именем "{имя_приложения}". Отправьте интерактивный запрос авторизации для этого пользователя и ресурса.)| Необходимо сначала получить согласие пользователя. Если вы не используете .NET Core (без веб-интерфейса), вызовите (только один раз) `AcquireTokeninteractive`. Если вы используете .NET Core или не хотите выполнять `AcquireTokenInteractive`, пользователь может переходить по URL-адресу, чтобы предоставить согласие: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read. для вызова `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: пользователю необходимо использовать многофакторную проверку подлинности (MFA).| Устранение рисков отсутствует. Если MFA настроен для клиента и Azure Active Directory (AAD) решает его применять, необходимо выполнить откат к интерактивному потоку, например `AcquireTokenInteractive` или `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: тип предоставления не поддерживается в конечных точках *"/Common"* или */консумерс* . Use the */organizations* or tenant-specific endpoint. You used */common*. (Тип предоставления не поддерживается через конечные точки /common или /consumers. Используйте конечную точку /organizations или конечную точку конкретного клиента. Вы использовали /common).| Как описано в сообщении от AAD, центр должен иметь клиента или конечную точку */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: текст запроса должен содержать следующий параметр: `client_secret or client_assertion`.| Это исключение может возникнуть, если приложение не было зарегистрировано как общедоступное клиентское приложение в Azure AD. В портал Azure измените манифест приложения и задайте для `allowPublicClient` значение `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: не удалось распознать вошедший в систему пользователь| Библиотеке не удалось запросить Текущий вошедший в систему пользователь Windows или этот пользователь не подключен к AD или AAD (пользователи, присоединенные к рабочему месту, не поддерживаются). Устранение рисков 1. в UWP убедитесь, что приложение имеет следующие возможности: Аутентификация предприятия, частные сети (клиент и сервер), сведения об учетной записи пользователя. Устранение рисков 2. реализуйте собственную логику для выборки имени пользователя (например, john@contoso.com) и используйте форму `AcquireTokenByIntegratedWindowsAuth`, которая принимает имя пользователя.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Этот метод использует протокол, предоставляемый Active Directory (AD). Если пользователь был создан в Azure Active Directory без дублирующей записи в AD ("управляемого" пользователя), этот метод завершится ошибкой. Пользователи, созданные в AD и продублированные в AAD ("федеративные" пользователи), могут использовать такой неинтерактивный метод аутентификации. Устранение. Используйте интерактивную проверку подлинности.|

### `MsalUiRequiredException`

Один из распространенных кодов состояния, возвращаемых из MSAL.NET при вызове `AcquireTokenSilent()`, — `MsalError.InvalidGrantError`. Этот код состояния означает, что приложение должно повторно вызывать библиотеку проверки подлинности, но в интерактивном режиме (Аккуиретокенинтерактиве или Аккуиретокенбидевицекодефлов для общедоступных клиентских приложений и выполнять трудности в веб-приложениях). Это связано с тем, что перед выдаче маркера проверки подлинности требуется дополнительное взаимодействие с пользователем.

В большинстве случаев происходит сбой `AcquireTokenSilent`, так как кэш маркеров не содержит токены, соответствующие запросу. Срок действия маркеров доступа истекает через 1 час, `AcquireTokenSilent` попытается получить новый объект на основе маркера обновления (в OAuth2 терминах это поток "обновление маркера"). Этот поток также может завершиться сбоем по различным причинам, например, если администратор клиента настраивает более строгие политики входа в систему. 

Взаимодействие нацелено на то, что пользователь выполняет действие. Некоторые из этих условий легко разрешить пользователям (например, принять условия использования одним щелчком), и некоторые из них не могут быть разрешены с текущей конфигурацией (например, компьютер должен подключаться к определенной корпоративной сети). Некоторые пользователи помогают настроить многофакторную проверку подлинности или установить Microsoft Authenticator на устройстве.

### <a name="msaluirequiredexception-classification-enumeration"></a>Перечисление `MsalUiRequiredException` классификации

MSAL предоставляет поле `Classification`, которое можно прочитать, чтобы обеспечить более удобный пользовательский интерфейс, например сообщить пользователю о том, что срок действия пароля истек или что ему нужно предоставить согласие на использование некоторых ресурсов. Поддерживаемые значения являются частью перечисления `UiRequiredExceptionClassification`.

| классификация;    | Значение           | Рекомендуемая обработка |
|-------------------|-------------------|----------------------|
| басикактион | Условие может быть разрешено путем взаимодействия с пользователем во время интерактивного потока проверки подлинности. | Вызовите Аккуиретокенинтерактивели (). |
| аддитионалактион | Условие может быть разрешено дополнительным удаленным носителем с системой за пределами интерактивного потока проверки подлинности. | Вызовите Аккуиретокенинтерактивели (), чтобы отобразить сообщение с объяснением действия по воссозданию носителя. Вызывающее приложение может скрыть потоки, требующие additional_action, если пользователь вряд ли будет выполнять действие по восвыполнению. |
| мессажеонли      | В данный момент невозможно разрешить условие. При запуске интерактивного потока проверки подлинности отобразится сообщение с объяснением условия. | Вызовите Аккуиретокенинтерактивели (), чтобы отобразить сообщение с объяснением условия. Аккуиретокенинтерактивели () возвращает ошибку Усерканцелед после того, как пользователь прочитает сообщение и закроет окно. Вызывающее приложение может скрыть потоки, которые приводят к message_only, если пользователь вряд ли будет использовать это сообщение.|
| консентрекуиред  | Согласие пользователя отсутствует или отменено. | Вызовите Аккуиретокенинтерактивели () для пользователя, чтобы предоставить согласие. |
| усерпассвордекспиред | Срок действия пароля пользователя истек. | Вызовите Аккуиретокенинтерактивели (), чтобы пользователь мог сбросить свой пароль. |
| промптневерфаилед| Интерактивная проверка подлинности была вызвана с параметром Prompt = никогда, принудительно MSAL использовать файлы cookie браузера, а не отображать браузер. Это не удалось. | Вызов Аккуиретокенинтерактивели () без запроса. нет |
| аккуиретокенсилентфаилед | Пакет SDK для MSAL не содержит достаточно сведений для получения маркера из кэша. Это может быть вызвано тем, что в кэше отсутствуют маркеры или учетная запись не найдена. В сообщении об ошибке содержатся дополнительные сведения.  | Вызовите Аккуиретокенинтерактивели (). |
| Нет    | Дополнительные сведения не предоставляются. Условие может быть разрешено путем взаимодействия с пользователем во время интерактивного потока проверки подлинности. | Вызовите Аккуиретокенинтерактивели (). |

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

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

MSAL. js предоставляет объекты ошибок, которые являются абстрактными и классифицируют различные типы распространенных ошибок. Он также предоставляет интерфейс для доступа к конкретным сведениям об ошибках, таким как сообщения об ошибке для их правильной работы.

### <a name="error-object"></a>Объект Error

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
- `AuthError.message`: то же, что и `errorMessage`.
- `AuthError.stack`: трассировка стека для ошибок, вызванных ошибками.

### <a name="error-types"></a>Типы ошибок

Ниже приведены доступные типы ошибок.

- `AuthError`: базовый класс ошибок для библиотеки MSAL. js, который также используется для непредвиденных ошибок.

- класс `ClientAuthError`: Error, который обозначает проблемы с проверкой подлинности клиента. К типу ClientAuthErrors относятся почти все ошибки, возникающие в этой библиотеке. Эти ошибки возникают из-за вызова метода входа, когда уже выполняется вход, пользователь отменяет имя входа и т. д.

- `ClientConfigurationError`: класс Error, расширяет `ClientAuthError`, вызываемые перед выполнением запросов, когда заданные параметры пользовательской конфигурации имеют неправильный формат или отсутствуют.

- класс `ServerError`: error представляет строки ошибок, отправленные сервером аутентификации. Это могут быть такие ошибки, как недопустимый формат запроса или недопустимые параметры, а также любые другие ошибки, не позволяющие серверу выполнить аутентификацию или авторизацию пользователя.

- класс `InteractionRequiredAuthError`: Error расширяет `ServerError`, чтобы представлять ошибки сервера, требующие интерактивного вызова. Эта ошибка возникает `acquireTokenSilent`, если пользователю необходимо взаимодействовать с сервером для предоставления учетных данных или согласия на проверку подлинности или авторизацию. Коды ошибок включают `"interaction_required"`, `"login_required"`и `"consent_required"`.

Для обработки ошибок в потоках проверки подлинности с помощью методов перенаправления (`loginRedirect`, `acquireTokenRedirect`) необходимо зарегистрировать обратный вызов, который вызывается с успешным или неудачным после перенаправления с помощью метода `handleRedirectCallback()` следующим образом:

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

При попытке использовать Неинтерактивный метод получения маркера, например `acquireTokenSilent`, возвращается ошибка, но MSAL не может сделать это автоматически.

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

## <a name="pythontabpython"></a>[Python](#tab/python)

В MSAL для Python большинство ошибок передаются как возвращаемое значение из вызова API. Эта ошибка представлена в виде словаря, содержащего ответ JSON от платформы Microsoft Identity.

* Успешный ответ содержит ключ `"access_token"`. Формат ответа определяется протоколом OAuth2. Дополнительные сведения см. в статье [5,1 (успешный ответ](https://tools.ietf.org/html/rfc6749#section-5.1) ).
* Ответ об ошибке содержит `"error"` и обычно `"error_description"`. Формат ответа определяется протоколом OAuth2. Дополнительные сведения см. в разделе [5,2. ответ об ошибке](https://tools.ietf.org/html/rfc6749#section-5.2)

При возвращении ошибки ключ `"error_description"` содержит удобное для чтения сообщение. который, в свою очередь, обычно содержит код ошибки платформы Microsoft Identity. Дополнительные сведения о различных кодах ошибок см. в разделе [коды ошибок проверки подлинности и авторизации](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes).

В MSAL для Python исключения редки, поскольку большинство ошибок обрабатывается возвратом значения ошибки. Исключение `ValueError` возникает только в том случае, если возникла ошибка при попытке использования библиотеки, например, если параметры API имеют неправильный формат.

## <a name="javatabjava"></a>[Java](#tab/java)

В MSAL для Java существуют три типа исключений: `MsalClientException`, `MsalServiceException`и `MsalInteractionRequiredException`. все, которые наследуют от `MsalException`.

- `MsalClientException` возникает при возникновении ошибки, которая является локальной для библиотеки или устройства.
- `MsalServiceException` возникает, когда служба маркеров безопасности (STS) возвращает сообщение об ошибке или возникает другая ошибка сети.
- `MsalInteractionRequiredException` возникает, когда для проверки подлинности требуется взаимодействие пользовательского интерфейса.

### <a name="msalserviceexception"></a>мсалсервицеексцептион

`MsalServiceException` предоставляет HTTP-заголовки, возвращаемые в запросах к STS. Доступ к ним через `MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>мсалинтерактионрекуиредексцептион

Один из распространенных кодов состояния, возвращаемых из MSAL для Java при вызове `AcquireTokenSilently()` `InvalidGrantError`. Это означает, что перед выдаче маркера проверки подлинности требуется дополнительное взаимодействие с пользователем. Приложение должно снова вызвать библиотеку проверки подлинности, но в интерактивном режиме, отправив `AuthorizationCodeParameters` или `DeviceCodeParameters` для общедоступных клиентских приложений.

В большинстве случаев, когда `AcquireTokenSilently` происходит сбой, это обусловлено тем, что кэш маркеров не содержит маркер, соответствующий запросу. Срок действия маркеров доступа истекает через один час, `AcquireTokenSilently` попытается получить новый на основе маркера обновления. В OAuth2 терминах это поток маркеров обновления. Этот поток также может завершиться сбоем по различным причинам, например, когда администратор клиента настраивает более строгие политики входа в систему.

Некоторые условия, которые приводят к этой ошибке, легко устранить пользователи. Например, им может потребоваться принять условия использования. Или, возможно, запрос не может быть выполнен с текущей конфигурацией, так как компьютер должен подключаться к определенной корпоративной сети.

MSAL предоставляет поле `reason`, которое можно использовать для повышения удобства работы пользователей. Например, поле `reason` может сообщить пользователю о том, что срок действия пароля истек или что ему необходимо предоставить согласие на использование некоторых ресурсов. Поддерживаемые значения являются частью перечисления `InteractionRequiredExceptionReason`.

| Причина | Значение | Рекомендуемая обработка |
|---------|-----------|-----------------------------|
| `BasicAction` | Условие может быть разрешено при взаимодействии с пользователем во время интерактивной проверки подлинности | Вызов `acquireToken` с интерактивными параметрами |
| `AdditionalAction` | Условие может быть разрешено дополнительным удаленным носителем с системой за пределами интерактивного потока проверки подлинности. | Вызовите `acquireToken` с интерактивными параметрами, чтобы отобразить сообщение с объяснением действия, которое необходимо выполнить. Вызывающее приложение может скрыть потоки, требующие дополнительных действий, если пользователь вряд ли завершит действие по воссозданию. |
| `MessageOnly` | В данный момент невозможно разрешить условие. Запустите интерактивный поток проверки подлинности, чтобы отобразить сообщение, объясняющее условие. | Вызовите `acquireToken` с интерактивными параметрами, чтобы отобразить сообщение с объяснением условия. `acquireToken` выдаст ошибку `UserCanceled` после того, как пользователь прочитает сообщение и закроет окно. Приложение может скрыть потоки, которые приводят к появлению сообщения, если пользователь вряд ли попытается воспользоваться этим сообщением. |
| `ConsentRequired`| Согласие пользователя отсутствует или отменено. |Вызовите `acquireToken` с интерактивными параметрами, чтобы пользователь мог предоставить согласие. |
| `UserPasswordExpired` | Срок действия пароля пользователя истек. | Вызов `acquireToken` с интерактивным параметром, чтобы пользователь мог сбросить свой пароль |
| `None` |  Дополнительные сведения приведены ниже. Условие может быть разрешено путем взаимодействия с пользователем во время интерактивного потока проверки подлинности. | Вызов `acquireToken` с интерактивными параметрами |

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

## <a name="iosmacostabiosmacos"></a>[iOS и macOS](#tab/iosmacos)

Полный список MSAL для iOS и macOS Errors приведен в [мсалеррор enum](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Все ошибки, вызванные MSAL, возвращаются с доменом `MSALErrorDomain`.

Для системных ошибок MSAL возвращает исходный `NSError` из системного API. Например, если получение маркера завершается неудачей из-за нехватки сетевого подключения, MSAL возвращает ошибку с доменом `NSURLErrorDomain` и `NSURLErrorNotConnectedToInternet` кодом.

На стороне клиента рекомендуется выполнять обработку по крайней мере двух следующих ошибок MSAL:

- `MSALErrorInteractionRequired`: пользователь должен выполнить интерактивный запрос. Существует множество условий, которые могут привести к такой ошибке, как просроченный сеанс проверки подлинности или необходимость дополнительных требований к проверке подлинности. Для восстановления вызовите интерактивный API приобретения токена MSAL. 

- `MSALErrorServerDeclinedScopes`: были отклонены некоторые или все области. Решите, следует ли продолжать работу только с предоставленными областями, или приостанавливаете процесс входа.

> [!NOTE]
> Перечисление `MSALInternalError` следует использовать только для справки и отладки. Не пытайтесь автоматически обрабатывайте эти ошибки во время выполнения. Если в приложении обнаруживаются какие-либо ошибки, которые попадают в `MSALInternalError`, может потребоваться отобразить общее сообщение пользователя, объясняющее, что произошло.

Например, `MSALInternalErrorBrokerResponseNotReceived` означает, что пользователь не завершил проверку подлинности и вручную возвращался в приложение. В этом случае приложение должно показывать общее сообщение об ошибке, объясняющее, что проверка подлинности не завершена, и предложит повторить проверку подлинности.

В следующем примере кода цели-C демонстрируются рекомендации по обработке некоторых распространенных условий возникновения ошибок.

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

При получении маркеров в автоматическом режиме приложения могут получать ошибки, когда для API-интерфейса, к которому вы пытаетесь получить доступ, требуется [запрос на утверждение условного доступа](conditional-access-dev-guide.md) , например политика mfa.

Шаблон для обработки этой ошибки — интерактивное получение маркера с помощью MSAL. Интерактивное получение токена передает запрос пользователю и предоставляет ему возможность выполнить требования политики условного доступа.

В некоторых случаях при вызове API, требующих условного доступа, вы можете получать запрос утверждений прямо в полученной от API ошибке. Например, если политика условного доступа должна иметь управляемое устройство (Intune), ошибка будет примерно такой, как [AADSTS53000: устройство должно быть управляемым для доступа к этому ресурсу](reference-aadsts-error-codes.md) или что-то подобное. В этом случае вы можете передать утверждения в запросе на получение токена, чтобы пользователь получил приглашение выполнить требования политики.

### <a name="net"></a>.NET

При вызове API, который требует условного доступа через MSAL.NET, приложение должно обрабатывать исключения, связанные с запросом утверждений. Они передаются в виде [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) с непустым свойством [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet).

Для обработки запроса на утверждение необходимо использовать метод `.WithClaim()` класса `PublicClientApplicationBuilder`.

### <a name="javascript"></a>JavaScript

При получении маркеров в автоматическом режиме (с помощью `acquireTokenSilent`) при использовании MSAL. js приложение может получать ошибки, если для API-интерфейса, к которому вы пытаетесь получить доступ, требуется [запрос на утверждение условного доступа](conditional-access-dev-guide.md) , например политика mfa.

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

При вызове API, требующих условного доступа, вы можете получать запрос утверждений прямо в полученной от API ошибке. В этом случае можно передать утверждения, возвращенные в сообщении об ошибке, в `claimsRequest` поле класса `AuthenticationParameters.ts`, чтобы удовлетворить соответствующую политику. 

Дополнительные сведения см. в статье [запрос дополнительных утверждений](active-directory-optional-claims.md) .

### <a name="msal-for-ios-and-macos"></a>MSAL для iOS и MacOS

MSAL для iOS и macOS позволяет запрашивать определенные утверждения как в интерактивном, так и в неавтоматическом сценарии получения маркеров.

Чтобы запросить настраиваемые утверждения, укажите `claimsRequest` в `MSALSilentTokenParameters` или `MSALInteractiveTokenParameters`.

Дополнительные сведения см. [в статье запрос пользовательских утверждений с помощью MSAL для iOS и macOS](request-custom-claims.md) .

## <a name="retrying-after-errors-and-exceptions"></a>Повторные попытки после ошибок и исключений

Вы должны реализовать собственные политики повтора при вызове MSAL. MSAL выполняет HTTP-вызовы к службе AAD, и иногда могут возникать сбои, например сеть может быть остановлена или сервер перегружен.  

### <a name="http-error-codes-500-600"></a>Коды ошибок HTTP 500–600

MSAL.NET реализует простой механизм однократного повтора для всех ошибок с кодами HTTP 500–600.

### <a name="http-429"></a>HTTP 429

Когда сервер маркеров службы (STS) перегружается с слишком большим количеством запросов, он возвращает ошибку HTTP 429 с указанием времени, пока вы не сможете повторить попытку в поле ответа `Retry-After`.

### <a name="net"></a>.NET

[Мсалсервицеексцептион](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) поверхностей `System.Net.Http.Headers.HttpResponseHeaders` как свойство `namedHeaders`. Чтобы повысить надежность приложений, можно использовать дополнительные сведения из кода ошибки. В описанном случае можно использовать `RetryAfterproperty` (типа `RetryConditionHeaderValue`) и COMPUTE, когда следует повторить попытку.

Ниже приведен пример для управляющего приложения, использующего поток учетных данных клиента. Это можно адаптировать к любому из методов получения маркера.

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
