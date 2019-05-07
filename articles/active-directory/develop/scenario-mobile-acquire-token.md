---
title: Мобильное приложение, что вызовы веб-API — получение токена для приложения | Платформы удостоверений Microsoft
description: Узнайте, как создать мобильное приложение, которое вызывает веб-API (получение токена для приложения)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6933bfbbff574495655ef9065a786fa313b02bd6
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075180"
---
# <a name="mobile-app-that-calls-web-apis---acquire-a-token"></a>Мобильное приложение, которое вызывает веб-интерфейсы API — получение маркера

Перед началом вызова защищенного веб-API, приложение потребуется маркер доступа. В этом разделе рассматривается процесс получения маркера с использованием библиотеки аутентификации Майкрософт (MSAL).

## <a name="scopes-to-request"></a>Чтобы запросить области

При запросе маркеров, области всегда является обязательным. Область определяет, какие данные, приложение может обращаться к.  

Самым простым подходом является сочетание нужный веб-интерфейса API `App ID URI` с областью `.default`. Это говорит Microsoft identity, что вашему приложению требуется настроить на портале все области.

Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

iOS
```swift
let scopes: [String] = ["https://graph.microsoft.com/.default"]
```

Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="acquiring-tokens"></a>Приобретения токенов

### <a name="via-msal"></a>с помощью MSAL

MSAL позволяет приложениям получать маркеры, в автоматическом режиме и интерактивном режиме. Просто вызывать эти методы и MSAL обратно возвращает маркер доступа для области, запрашиваемые. Правильный шаблон — это выполнить автоматический запрос и переход к интерактивный запрос.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently
// Result is in our silent callback (success or error)
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or >1 account */
        }
    }
});    

[...]

// No accounts found, interactively request a token 
// TODO: Create an interactive callback to catch successful or failed request
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

```swift
// Initialize our app 
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)

// Get tokens
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
    if let error = error {
        let nsError = error as NSError

        // interactionRequired means we need to ask the user to sign-in. This usually happens
        // when the user's Refresh Token is expired or if the user has changed their password
        // among other possible reasons.
        if (nsError.domain == MSALErrorDomain) {
            if (nsError.code == MSALError.interactionRequired.rawValue) {    
                DispatchQueue.main.async {
                    guard let applicationContext = self.applicationContext else { return }
                    let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
                    applicationContext.acquireToken(with: parameters) { (result, error) in
                        if let error = error {
                            self.updateLogging(text: "Could not acquire token: \(error)")
                            return
                        }

                        guard let result = result else {
                            self.updateLogging(text: "Could not acquire token: No result returned")
                            return
                        }
                        
                        // Token is ready via interaction!
                        self.accessToken = result.accessToken
                    }
                }
                return
            }
        }

        self.updateLogging(text: "Could not acquire token silently: \(error)")
        return
    }
    guard let result = result else {
        self.updateLogging(text: "Could not acquire token: No result returned")
        return
    }

    // Token is ready via silent acquisition 
    self.accessToken = result.accessToken
}
```

#### <a name="xamarin"></a>Xamarin

```CSharp
string[] scopes = new string["https://graph.microsoft.com/.default"];
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException e)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="via-protocol"></a>с помощью протокола

Мы не рекомендуем переход непосредственно для соответствующего протокола. Ваше приложение не сможет единый вход (SSO) большинства и не будет поддерживать все управление устройствами и сценариев условного доступа.

При получении маркеров для мобильных приложений с помощью протокола, вам потребуется для выполнения запросов в 2: получение кода авторизации и его обмена на маркер. 

#### <a name="getting-authorization-code"></a>Получение кода авторизации

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="getting-access-and-refresh-token"></a>Получение маркера доступа и обновления

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
