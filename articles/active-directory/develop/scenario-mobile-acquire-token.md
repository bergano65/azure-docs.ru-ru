---
title: Приобретите токен для вызова веб-API (мобильные приложения) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как создать мобильное приложение, которое вызывает web-апоттестаты. (Получить маркер для приложения.)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 9427235f47a31da75426559a4285634ab2837577
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132460"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Получите токен для мобильного приложения, которое вызывает web-аБО

Прежде чем приложение сможет вызывать защищенные web-aIS, ему нужен токен доступа. Эта статья рассказывает о вас, чтобы получить токен с помощью библиотеки подлинности Майкрософт (MSAL).

## <a name="define-a-scope"></a>Определение области

При запросе маркера необходимо определить область действия. Область определяет, к каким данным может получить доступ приложение.  

Самый простой способ определить область действия — объединить `App ID URI` нужный `.default`web API с областью. Это определение сообщает платформе идентификации Майкрософт, что ваше приложение требует всех областей, которые установлены на портале.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Получить токены

### <a name="acquire-tokens-via-msal"></a>Приобретение токенов через MSAL

MSAL позволяет приложениям приобретать токены молча и интерактивно. При `AcquireTokenSilent()` вызове `AcquireTokenInteractive()`или, MSAL возвращает токен доступа для запрошенных областей. Правильный шаблон заключается в том, чтобы сделать молчаливый запрос, а затем вернуться к интерактивному запросу.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});    

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

Сначала попробуйте получить токен молча:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";
    
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];
    
MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {
        
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        // Access token to call the Web API
        NSString *accessToken = result.accessToken;
    }
        
    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```
 
```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"
        
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in
            
    guard let authResult = result, error == nil else {
                
    let nsError = error! as NSError
                
    if (nsError.domain == MSALErrorDomain &&
        nsError.code == MSALError.interactionRequired.rawValue) {
                    
            // Interactive auth will be required, call acquireToken()
            return
         }
         return
     }
            
    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

Если MSAL `MSALErrorInteractionRequired`возвращается, попробуйте приобрести токены в интерактивном режиме:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error) 
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        NSString *accessToken = result.accessToken;
    }
}];
```

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in
                
    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }
                
    // Get access token from result
    let accessToken = authResult.accessToken
})
```

MSAL для iOS и macOS поддерживает различные модификаторы, чтобы получить токен интерактивно или бесшумно:
* [Общие параметры для получения токена](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Параметры для получения интерактивного токена](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Параметры для получения молчаливого токена](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

В следующем примере показан минимальный код для интерактивного получения маркера. В этом примере используется Microsoft Graph для чтения профиля пользователя.

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

#### <a name="mandatory-parameters-in-msalnet"></a>Обязательные параметры в MSAL.NET

`AcquireTokenInteractive`имеет только один `scopes`обязательный параметр: . Параметр `scopes` перечисляет строки, определяющие области, для которых требуется токен. Если токен предназначен для Microsoft Graph, вы можете найти необходимые области в ссылке API каждого API Microsoft Graph. В справке перейдите в раздел "Разрешения". 

Например, чтобы [перечислить контакты пользователя,](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)используйте область "User.Read", "Contacts.Read". Для получения дополнительной [информации](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)см.

На Android можно указать родительскую активность при `PublicClientApplicationBuilder`создании приложения с помощью . Если вы не указали родительское действие в то время, `.WithParentActivityOrWindow` позже вы можете указать его, используя, как в следующем разделе. Если вы указываете родительскую активность, то то маркер возвращается к родительской деятельности после взаимодействия. Если вы не укажете `.ExecuteAsync()` его, то вызов бросает исключение.

#### <a name="specific-optional-parameters-in-msalnet"></a>Конкретные дополнительные параметры в MSAL.NET

В следующих разделах разъясняется факультативные параметры в MSAL.NET. 

##### <a name="withprompt"></a>СПромтом

Параметр `WithPrompt()` контролирует интерактивность с пользователем, указывая запрос.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Класс определяет следующие константы:

- `SelectAccount`заставляет службу безопасности (STS) представить диалоговую будку выбора учетной записи. В поле диалога содержатся учетные записи, для которых пользователь имеет сеанс. Вы можете использовать эту опцию, когда вы хотите, чтобы пользователь выбрал среди различных идентификационных данных. Эта опция приводит `prompt=select_account` к отправке MSAL поставщику идентификационных данных. 
    
    Константа `SelectAccount` по умолчанию, и она эффективно обеспечивает наилучший опыт на основе имеющейся информации. Доступная информация может включать учетную запись, наличие сеанса для пользователя и так далее. Не изменяйте этот дефолт, если у вас нет веских причин, чтобы сделать это.
- `Consent`позволяет попросить пользователя дать согласие, даже если согласие было получено ранее. В этом случае MSAL `prompt=consent` отправляет поставщику идентификационных данных. 

    Возможно, вы захотите использовать `Consent` константу в приложениях, ориентированных на безопасность, где управление организацией требует, чтобы пользователи видели диалоговое окно согласия каждый раз, когда они используют приложение.
- `ForceLogin`позволяет службе подсказывать пользователю учетные данные, даже если запрос не нужен. 

    Эта опция может быть полезна, если приобретение токенов не удается, и вы хотите, чтобы пользователь войти снова. В этом случае MSAL `prompt=login` отправляет поставщику идентификационных данных. Эту опцию можно использовать в приложениях, ориентированных на безопасность, где управление организации требует, чтобы пользователь входе в систему каждый раз, когда он получает доступ к определенным частям приложения.
- `Never`только для .NET 4.5 и Windows Runtime (WinRT). Эта константа не подскажет пользователю, но попытается использовать файл cookie, хранящийся в скрытом встроенном веб-виде. Для получения дополнительной информаци [MSAL.NETи см.](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers)

    Если эта опция `AcquireTokenInteractive` не удается, а затем бросает исключение, чтобы уведомить вас о необходимости взаимодействия с uI. Затем вам нужно `Prompt` использовать другой параметр.
- `NoPrompt`не отправляет запрос поставщику идентификационных данных. 

    Эта опция полезна только для политики для отсылки в Azure Active Directory B2C. Для получения дополнительной информации, см [B2C специфики](https://aka.ms/msal-net-b2c-specificities).

##### <a name="withextrascopetoconsent"></a>СЭкстраскопто-согласие

Используйте `WithExtraScopeToConsent` модификатор в расширенном сценарии, где вы хотите, чтобы пользователь предоставил предварительное согласие на несколько ресурсов. Этот модификатор можно использовать, если вы не хотите использовать постепенное согласие, которое обычно используется с MSAL.NET или платформой идентификации Microsoft 2.0. Для получения дополнительной информации [см.](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)

Приведем пример кода: 

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Другие дополнительные параметры

Чтобы узнать о других `AcquireTokenInteractive`дополнительных [reference documentation for AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)параметрах для, см.

### <a name="acquire-tokens-via-the-protocol"></a>Приобретение токенов через протокол

Мы не рекомендуем напрямую использовать протокол для получения токенов. Если вы это сделаете, то приложение не будет поддерживать некоторые сценарии, которые включают в себя один вход (SSO), управление устройствами и условный доступ.

При использовании протокола для получения токенов для мобильных приложений делайте два запроса: 

* Получите код авторизации.
* Обменкодивать код на токен.

#### <a name="get-an-authorization-code"></a>Получение кода авторизации

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Получите доступ и обновите токен

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Вызов веб-API](scenario-mobile-call-api.md)
