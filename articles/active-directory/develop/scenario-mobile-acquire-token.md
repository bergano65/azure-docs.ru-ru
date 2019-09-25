---
title: Мобильное приложение, вызывающее веб-API — получение маркера для приложения | Платформа Microsoft Identity
description: Узнайте, как создать мобильное приложение, вызывающее веб-API (получение маркера для приложения).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 454d62f871290d2e7dd8d0eee4b1a2429625a5fc
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268268"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>Мобильное приложение, вызывающее веб-API — получение токена

Прежде чем начать вызов защищенных веб-API, приложению потребуется маркер доступа. В этой статье описывается процесс получения маркера с помощью библиотеки проверки подлинности Майкрософт (MSAL).

## <a name="scopes-to-request"></a>Области для запроса

При запросе маркера необходимо определить область. Область определяет, к каким данным имеет доступ приложение.  

Самый простой подход заключается в объединении требуемого веб- `App ID URI` API с областью. `.default` Это указывает платформе удостоверений Майкрософт, что приложению требуются все области, установленные на портале.

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Получить токены

### <a name="acquire-tokens-via-msal"></a>Получение маркеров через MSAL

MSAL позволяет приложениям получать маркеры автоматически и интерактивно. Просто вызовите эти методы и MSAL возвращает маркер доступа для запрошенных областей. Правильный шаблон — выполнить автоматический запрос и вернуться к интерактивному запросу.

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
// TODO: Create an interactive callback to catch successful or failed request.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

**Сначала попытайтесь получить маркер автоматически:**

Objective-C.

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
 
SWIFT

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

**Затем, если MSAL `MSALErrorInteractionRequired`возвращает, попробуйте получить маркеры в интерактивном режиме:**

Objective-C.

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

SWIFT

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

MSAL для iOS и macOS поддерживает различные модификаторы при получении маркера в интерактивном или автоматическом режиме.
* [Общие параметры при получении токена](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Параметры для получения интерактивного маркера](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Параметры для получения автоматического токена](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

В следующем примере показан минимальный код для интерактивного получения маркера для чтения профиля пользователя с Microsoft Graph.

```CSharp
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

`AcquireTokenInteractive`имеет только один обязательный ``scopes``параметр, который содержит перечисление строк, определяющих области, для которых требуется токен. Если маркер предназначен для Microsoft Graph, необходимые области можно найти в справочнике по API для каждого API Microsoft Graph в разделе "разрешения". Например, чтобы получить [список контактов пользователя](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), необходимо будет использовать область "пользователь. чтение", "Contacts. Read". См. также [Microsoft Graph ссылки на разрешения](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

Если вы не указали его при создании приложения, в Android необходимо также указать родительское действие (с помощью `.WithParentActivityOrWindow`, см. ниже), чтобы токен был возвращен к родительскому действию после взаимодействия. Если не указать его, при вызове `.ExecuteAsync()`будет создано исключение.

#### <a name="specific-optional-parameters-in-msalnet"></a>Определенные необязательные параметры в MSAL.NET

##### <a name="withprompt"></a>виспромпт

`WithPrompt()`используется для управления интерактивностью пользователя путем указания запроса

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Класс определяет следующие константы:

- ``SelectAccount``: заставит STS предоставить диалоговое окно выбора учетной записи, содержащее учетные записи, для которых у пользователя есть сеанс. Этот параметр полезен в тех случаях, когда разработчики приложений хотят позволить пользователю выбирать из разных удостоверений. Этот параметр позволяет MSAL отправить ``prompt=select_account`` поставщику удостоверений. Этот параметр используется по умолчанию, и он обеспечивает оптимальную работу на основе доступных сведений (учетной записи, присутствия сеанса для пользователя и т. д.). ...). Не изменяйте его, если у вас нет веских причин.
- ``Consent``: позволяет разработчику приложения принудительно запрашивать согласие пользователя, даже если было предоставлено согласие. В этом случае MSAL отправляет `prompt=consent` поставщику удостоверений. Этот параметр можно использовать в некоторых приложениях безопасности, в которых Управление организацией требует, чтобы пользователь предвидит диалоговое окно согласия при каждом использовании приложения.
- ``ForceLogin``: позволяет разработчику приложения получать запрос учетных данных от службы, даже если этот пользователь не нужен. Этот параметр может быть полезен в случае сбоя при получении маркера, чтобы пользователь мог повторно выполнить вход. В этом случае MSAL отправляет `prompt=login` поставщику удостоверений. Опять же, мы видели, что он использовался в некоторых приложениях, ориентированных на безопасность, где управление Организацией требует, чтобы пользователь повторно вошел в систему при каждом доступе к определенным частям приложения.
- ``Never``(только для .NET 4,5 и WinRT) не запрашивает пользователя, а вместо этого будет пытаться использовать файл cookie, хранящийся в скрытом внедренном веб-представлении (см. ниже: Веб-представления в MSAL.NET). При использовании этого параметра может произойти сбой, и в `AcquireTokenInteractive` этом случае будет создано исключение, уведомляющее о необходимости взаимодействия пользовательского интерфейса, и необходимо использовать другой `Prompt` параметр.
- ``NoPrompt``. Не отправляет запрос поставщику удостоверений. Этот параметр полезен только для Azure AD B2C редактирования политик профилей (см. раздел [особенности B2C](https://aka.ms/msal-net-b2c-specificities)).

##### <a name="withextrascopetoconsent"></a>висекстраскопетоконсент

Этот модификатор используется в расширенном сценарии, где пользователь должен заранее согласиться с несколькими ресурсами (и вы не хотите использовать добавочное согласие, которое обычно используется с MSAL.NET/платформой Microsoft Identity Platform v 2.0). Дополнительные сведения см. в разделе [как получить согласие пользователя на получение нескольких ресурсов](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Другие необязательные параметры

Дополнительные сведения о других необязательных параметрах `AcquireTokenInteractive` см. в справочной документации по [аккуиретокенинтерактивепараметербуилдер](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods) .

### <a name="acquire-tokens-via-the-protocol"></a>Получение маркеров через протокол

Мы не советуем использовать протокол напрямую. В этом случае приложение не будет поддерживать некоторые возможности единого входа (SSO), управления устройствами и условного доступа.

При использовании протокола для получения маркеров для мобильных приложений необходимо выполнить два запроса: получение кода авторизации и его обмен для маркера.

#### <a name="get-authorization-code"></a>Получить код авторизации

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>Получение маркера доступа и обновления

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

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Вызов веб-API](scenario-mobile-call-api.md)
