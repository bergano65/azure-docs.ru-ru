---
title: Ошибки и исключения (MSAL) | Платформа Microsoft Identity
description: Сведения об обработке ошибок и исключений, условном доступе и проблемах с утверждениями в MSAL приложениях.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: e6356b4f72f08afc2c5b3e5570086fd166a75216
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268618"
---
# <a name="handle-msal-exceptions-and-errors"></a>Обработку исключений и ошибок MSAL

В этой статье приводятся общие сведения о различных типах ошибок и рекомендации по обработке общих ошибок входа.

## <a name="msal-error-handling-basics"></a>Основные сведения об обработке ошибок MSAL

В библиотеке аутентификации Майкрософт (MSAL) применяются исключения, которые позволяют разработчикам приложений устранять проблемы самостоятельно, не отображая сообщения об ошибках пользователям. Сообщения об исключениях не локализованы.

При обработке ошибок и исключений вы можете различать их по типам исключений и кодам ошибки.  Список кодов ошибок аутентификации и авторизации можно найти [здесь](reference-aadsts-error-codes.md).

Во время автоматического или интерактивного получения маркера во время входа в систему приложения могут столкнуться с ошибками, такими как ошибки, связанные с отправкой, условный доступ (MFA, Управление устройствами, ограничения на основе расположения), выдача и активация маркера и пользователь свойства.

## <a name="msal-for-ios-and-macos-errors"></a>MSAL для ошибок iOS и macOS

Полный список ошибок содержится в [перечислении мсалеррор](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Все ошибки, вызванные MSAL, `MSALErrorDomain` возвращаются с использованием домена. 

Для системных ошибок MSAL возвращает исходный `NSError` интерфейс API системы. Например, если получение маркера завершается неудачей из-за нехватки сетевого подключения, MSAL возвращает `NSURLErrorDomain` ошибку с `NSURLErrorNotConnectedToInternet` доменом и кодом.

На стороне клиента рекомендуется выполнять обработку по крайней мере двух следующих ошибок MSAL:

- `MSALErrorInteractionRequired`. Пользователь должен выполнить интерактивный запрос. Существует множество условий, которые могут привести к такой ошибке, как просроченный сеанс проверки подлинности или необходимость дополнительных требований к проверке подлинности. Для восстановления вызовите интерактивный API приобретения токена MSAL. 

- `MSALErrorServerDeclinedScopes`. Некоторые области были отклонены. Решите, следует ли продолжать работу только с предоставленными областями, или приостанавливаете процесс входа.

> [!NOTE]
> `MSALInternalError` Перечисление следует использовать только для справки и отладки. Не пытайтесь автоматически обрабатывайте эти ошибки во время выполнения. Если в приложении обнаруживаются какие-либо ошибки, которые попадают в `MSALInternalError`, может потребоваться отобразить общее сообщение пользователя, объясняющее, что произошло.

Например, `MSALInternalErrorBrokerResponseNotReceived` это означает, что пользователь не завершил проверку подлинности и вручную возвращался в приложение. В этом случае приложение должно показывать общее сообщение об ошибке, объясняющее, что проверка подлинности не завершена, и предложит повторить проверку подлинности.

В следующем примере кода цели-C демонстрируются рекомендации по обработке некоторых распространенных условий возникновения ошибок.

Objective-C
```ObjC
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

Код SWIFT
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

## <a name="net-exceptions"></a>Исключения .NET

При обработке исключений вы можете различать их по типам исключений и элементу `ErrorCode`. `ErrorCode`значения являются константами типа [мсалеррор](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Вы также можете взглянуть на поля [мсалклиентексцептион](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [мсалсервицеексцептион](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)и [мсалуирекуиредексцептион](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Если создается [мсалсервицеексцептион](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) , попробуйте проверить [подлинность и коды ошибок авторизации](reference-aadsts-error-codes.md) , чтобы узнать, присутствует ли там код.

### <a name="common-exceptions"></a>Распространенные исключения

Ниже приведены распространенные исключения, которые могут быть вызваны, и некоторые возможные способы их устранения.  

| Исключение | Код ошибки | Предотвращение|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: The user or administrator has not consented to use the application with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource. (Пользователь или администратор не предоставили разрешение на использование приложения с идентификатором "{идентификатор_приложения}" и именем "{имя_приложения}". Отправьте интерактивный запрос авторизации для этого пользователя и ресурса.)| Необходимо сначала получить согласие пользователя. Если вы не используете .NET Core (без веб-интерфейса), вызовите (только один раз) `AcquireTokeninteractive`. Если вы используете .NET Core или не хотите делать это `AcquireTokenInteractive`, пользователь может переходить по URL-адресу, чтобы предоставить согласие:. https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read Для вызова `AcquireTokenInteractive`:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: Пользователь должен использовать многофакторную проверку подлинности (MFA).| Устранение рисков отсутствует. Если MFA настроено для клиента и Azure Active Directory (AAD) решает его применять, необходимо выполнить откат к интерактивному потоку, например `AcquireTokenInteractive` или. `AcquireTokenByDeviceCode`|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: Тип предоставления не поддерживается для конечных точек *"/Common"* или */консумерс* . Use the */organizations* or tenant-specific endpoint. You used */common*. (Тип предоставления не поддерживается через конечные точки /common или /consumers. Используйте конечную точку /organizations или конечную точку конкретного клиента. Вы использовали /common).| Как описано в сообщении от AAD, центр должен иметь клиента или конечную точку */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: Текст запроса должен содержать следующий параметр: `client_secret or client_assertion`.| Это исключение может возникнуть, если приложение не было зарегистрировано как общедоступное клиентское приложение в Azure AD. В портал Azure измените манифест приложения и задайте для `allowPublicClient` `true`него значение. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`. Could not identify logged in user. (Не удалось идентифицировать пользователя, вошедшего в систему.)| Библиотеке не удалось запросить Текущий вошедший в систему пользователь Windows или этот пользователь не подключен к AD или AAD (пользователи, присоединенные к рабочему месту, не поддерживаются). Решение 1. Проверьте на UWP, что приложение имеет следующие возможности: корпоративная аутентификация, частные сети (клиент и сервер), данные учетной записи пользователя. Решение 2. Создайте собственную логику для получения имени пользователя (например, john@contoso.com) и примените форму `AcquireTokenByIntegratedWindowsAuth`, которая принимает имя пользователя.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Этот метод использует протокол, предоставляемый Active Directory (AD). Если пользователь был создан в Azure Active Directory без дублирующей записи в AD ("управляемого" пользователя), этот метод завершится ошибкой. Пользователи, созданные в AD и продублированные в AAD ("федеративные" пользователи), могут использовать такой неинтерактивный метод аутентификации. Решение. Используйте интерактивный режим аутентификации.|

### `MsalUiRequiredException`

Один из распространенных кодов состояния, возвращаемых из MSAL.NET `AcquireTokenSilent()` при `MsalError.InvalidGrantError`вызове, —. Этот код состояния означает, что приложение должно повторно вызывать библиотеку проверки подлинности, но в интерактивном режиме (Аккуиретокенинтерактиве или Аккуиретокенбидевицекодефлов для общедоступных клиентских приложений и выполнять трудности в веб-приложениях). Это связано с тем, что перед выдаче маркера проверки подлинности требуется дополнительное взаимодействие с пользователем.

В большинстве случаев `AcquireTokenSilent` происходит сбой, так как кэш маркеров не содержит токены, соответствующие запросу. Срок действия маркеров доступа истекает через 1 час `AcquireTokenSilent` и попытается получить новый объект на основе маркера обновления (в OAuth2 терминах это поток "обновить маркер"). Этот поток также может завершиться сбоем по различным причинам, например, если администратор клиента настраивает более строгие политики входа в систему. 

Взаимодействие нацелено на то, что пользователь выполняет действие. Некоторые из этих условий легко разрешить пользователям (например, принять условия использования одним щелчком), и некоторые из них не могут быть разрешены с текущей конфигурацией (например, компьютер должен подключаться к определенной корпоративной сети). Некоторые пользователи помогают настроить многофакторную проверку подлинности или установить Microsoft Authenticator на устройстве.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`Перечисление классификации

MSAL предоставляет `Classification` поле, которое можно прочитать, чтобы обеспечить лучшее взаимодействие с пользователем, например сообщить пользователю о том, что срок действия пароля истек или что ему необходимо предоставить согласие на использование некоторых ресурсов. Поддерживаемые значения являются частью `UiRequiredExceptionClassification` перечисления:

| Классификация    | Значение           | Рекомендуемая обработка |
|-------------------|-------------------|----------------------|
| басикактион | Условие может быть разрешено путем взаимодействия с пользователем во время интерактивного потока проверки подлинности. | Вызовите Аккуиретокенинтерактивели (). |
| аддитионалактион | Условие может быть разрешено дополнительным удаленным носителем с системой за пределами интерактивного потока проверки подлинности. | Вызовите Аккуиретокенинтерактивели (), чтобы отобразить сообщение с объяснением действия по воссозданию носителя. Вызывающее приложение может скрыть потоки, требующие additional_action, если пользователь вряд ли будет выполнять действие по восвыполнению. |
| мессажеонли      | В данный момент невозможно разрешить условие. При запуске интерактивного потока проверки подлинности отобразится сообщение с объяснением условия. | Вызовите Аккуиретокенинтерактивели (), чтобы отобразить сообщение с объяснением условия. Аккуиретокенинтерактивели () возвращает ошибку Усерканцелед после того, как пользователь прочитает сообщение и закроет окно. Вызывающее приложение может скрыть потоки, которые приводят к message_only, если пользователь вряд ли будет использовать это сообщение.|
| консентрекуиред  | Согласие пользователя отсутствует или отменено. | Вызовите Аккуиретокенинтерактивели () для пользователя, чтобы предоставить согласие. |
| усерпассвордекспиред | Срок действия пароля пользователя истек. | Вызовите Аккуиретокенинтерактивели (), чтобы пользователь мог сбросить свой пароль. |
| промптневерфаилед| Интерактивная проверка подлинности была вызвана с параметром Prompt = никогда, принудительно MSAL использовать файлы cookie браузера, а не отображать браузер. Это не удалось. | Вызов Аккуиретокенинтерактивели () без запроса. нет |
| аккуиретокенсилентфаилед | Пакет SDK для MSAL не содержит достаточно сведений для получения маркера из кэша. Это может быть вызвано тем, что в кэше отсутствуют маркеры или учетная запись не найдена. В сообщении об ошибке содержатся дополнительные сведения.  | Вызовите Аккуиретокенинтерактивели (). |
| Отсутствуют    | Дополнительные сведения не предоставляются. Условие может быть разрешено путем взаимодействия с пользователем во время интерактивного потока проверки подлинности. | Вызовите Аккуиретокенинтерактивели (). |

## <a name="code-example"></a>Примеры кода

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
   res = await application.AcquireTokenInteractive(scopes)
                          .ExecuteAsync();
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


## <a name="javascript-errors"></a>Ошибки JavaScript

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
* **AuthError.message:**  То же, что и errorMessage.
* **AuthError.stack:** Трассировка стека для возникающих ошибок. Позволяет отследить исходную точку возникновения ошибки.

### <a name="error-types"></a>Типы ошибок

Ниже приведены доступные типы ошибок.

- `AuthError`. Базовый класс ошибки в библиотеке MSAL.js, который также используется для непредвиденных ошибок.

- `ClientAuthError`. Класс Error, который обозначает проблемы с проверкой подлинности клиента. К типу ClientAuthErrors относятся почти все ошибки, возникающие в этой библиотеке. Эти ошибки возникают из-за вызова метода входа, когда уже выполняется вход, пользователь отменяет имя входа и т. д.

- `ClientConfigurationError`. Класс Error, расширяет `ClientAuthError` исключение, прежде чем запросы выполняются, если параметры пользовательской конфигурации имеют неправильный формат или отсутствуют.

- `ServerError`. Класс Error представляет строки ошибок, отправленные сервером проверки подлинности. Это могут быть такие ошибки, как недопустимый формат запроса или недопустимые параметры, а также любые другие ошибки, не позволяющие серверу выполнить аутентификацию или авторизацию пользователя.

- `InteractionRequiredAuthError`. Класс Error, расширяется `ServerError` для представления ошибок сервера, требующих интерактивного вызова. Эта ошибка возникает, `acquireTokenSilent` если пользователю необходимо взаимодействовать с сервером для предоставления учетных данных или согласия на проверку подлинности или авторизацию. Коды ошибок включают `"interaction_required"`в `"login_required"`себя, `"consent_required"`и.

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

Методы для всплывающих взаимодействий (`loginPopup`, `acquireTokenPopup`) возвращают обещания, поэтому вы можете использовать для их обработки шаблон обещания (.then и .catch), как показано далее:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Требуемое взаимодействие, ошибки

При попытке использовать Неинтерактивный метод получения маркера, такого как `acquireTokenSilent`, возвращается ошибка, но MSAL не может сделать это автоматически.

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

## <a name="conditional-access-and-claims-challenges"></a>Условный доступ и запросы утверждений

При получении маркеров в автоматическом режиме приложения могут получать ошибки, когда для API-интерфейса, к которому вы пытаетесь получить доступ, требуется [запрос на утверждение условного доступа](conditional-access-dev-guide.md) , например политика mfa.

При получении маркеров в автоматическом режиме приложения могут получать ошибки, когда для API-интерфейса, к которому вы пытаетесь получить доступ, требуется [запрос на утверждение условного доступа](conditional-access-dev-guide.md) , например политика mfa.

Шаблон для обработки этой ошибки — интерактивное получение маркера с помощью MSAL. Интерактивное получение токена передает запрос пользователю и предоставляет ему возможность выполнить требования политики условного доступа.

В некоторых случаях при вызове API, требующих условного доступа, вы можете получать запрос утверждений прямо в полученной от API ошибке. Например, если политика условного доступа требует использовать управляемое устройство (Intune), вы получите ошибку [AADSTS53000: Your device is required to be managed to access this resource](reference-aadsts-error-codes.md) (Требуется управляемое устройство для доступа к этому ресурсу) или что-то подобное. В этом случае вы можете передать утверждения в запросе на получение токена, чтобы пользователь получил приглашение выполнить требования политики.

### <a name="net"></a>.NET

При вызове API, который требует условного доступа через MSAL.NET, приложение должно обрабатывать исключения, связанные с запросом утверждений. Они передаются в виде [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) с непустым свойством [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet).

Для обработки запроса на утверждение необходимо использовать `.WithClaim()` метод `PublicClientApplicationBuilder` класса.

### <a name="javascript"></a>JavaScript

При автоматическом получении маркеров (с `acquireTokenSilent`помощью) с помощью MSAL. js приложение может получать ошибки, если для API-интерфейса, к которому вы пытаетесь получить доступ, требуется [запрос на утверждение условного доступа](conditional-access-dev-guide.md) , например политика mfa.

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

### <a name="msal-for-ios-and-macos"></a>MSAL для iOS и macOS

MSAL для iOS и macOS позволяет запрашивать определенные утверждения как в интерактивном, так и в неавтоматическом сценарии получения маркеров.

Чтобы запросить настраиваемые утверждения, укажите `claimsRequest` в `MSALSilentTokenParameters` или `MSALInteractiveTokenParameters`.

Дополнительные сведения см. [в статье запрос пользовательских утверждений с помощью MSAL для iOS и macOS](request-custom-claims.md) .

## <a name="retrying-after-errors-and-exceptions"></a>Повторные попытки после ошибок и исключений

Вы должны реализовать собственные политики повтора при вызове MSAL. MSAL выполняет HTTP-вызовы к службе AAD, и иногда могут возникать сбои, например сеть может быть остановлена или сервер перегружен.  

### <a name="http-error-codes-500-600"></a>Коды ошибок HTTP 500–600

MSAL.NET реализует простой механизм однократного повтора для всех ошибок с кодами HTTP 500–600.

### <a name="http-429"></a>HTTP 429

Когда сервер маркеров службы (STS) перегружается с слишком большим количеством запросов, он возвращает ошибку HTTP 429 с указанием времени, пока вы не сможете повторить попытку в `Retry-After` поле ответа.

### <a name="net"></a>.NET

[Мсалсервицеексцептион](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) поверхности `System.Net.Http.Headers.HttpResponseHeaders` в виде свойства `namedHeaders`. Чтобы повысить надежность приложений, можно использовать дополнительные сведения из кода ошибки. В описанном случае можно использовать `RetryAfterproperty` тип `RetryConditionHeaderValue`(of) и COMPUTE, когда следует повторить попытку.

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
