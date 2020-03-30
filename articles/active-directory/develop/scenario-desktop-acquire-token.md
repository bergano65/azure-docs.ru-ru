---
title: Приобретите токен для вызова веб-API (приложение для рабочего стола) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как создать настольное приложение, которое вызывает web-аБО для приобретения маркера для приложения
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 636c7c654b98ced5f93c3ace0e4a99bfc9bf7def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262611"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Настольное приложение, которое вызывает веб-AIS: Приобрести маркер

После того как вы создали экземпляр приложения общедоступного клиента, вы будете использовать его для приобретения маркера, который вы будете затем использовать для вызова веб-API.

## <a name="recommended-pattern"></a>Рекомендуемый шаблон

Веб-API определяется `scopes`его . Независимо от опыта, который вы предоставляете в вашем приложении, шаблон для использования:

- Систематически пытайтесь получить токен из кэша `AcquireTokenSilent`токенов, позвонив.
- Если этот вызов не `AcquireToken` удается, используйте поток, который `AcquireTokenXX`вы хотите использовать, который представлен здесь .

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>В MSAL.NET

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account  
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macos"></a>[Macos](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>В MSAL для iOS и macOS

Objective-C.

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift:

```swift
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
}
```
---

Вот различные способы приобретения токенов в настольном приложении.

## <a name="acquire-a-token-interactively"></a>Приобрести токен в интерактивном режиме

В следующем примере показан минимальный код для интерактивного доступа к маркеру для чтения профиля пользователя с помощью Microsoft Graph.

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>В MSAL.NET

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

### <a name="mandatory-parameters"></a>Обязательные параметры

`AcquireTokenInteractive`имеет только один ``scopes``обязательный параметр, который содержит перечисление строк, определяющих области, для которых требуется токен. Если токен предназначен для Microsoft Graph, необходимые области можно найти в ссылке API каждого API Microsoft Graphpi в разделе под названием "Разрешения". Например, для того, чтобы [перечислить контакты пользователя,](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)необходимо использовать область "User.Read", "Contacts.Read". Для получения дополнительной [информации](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)см.

На Android, вы также должны указать `.WithParentActivityOrWindow`родительскую деятельность с помощью, как показано на рисунке, так что маркер возвращается к этой родительской деятельности после взаимодействия. Если вы не указали его, при `.ExecuteAsync()`вызове выбрасывается исключение.

### <a name="specific-optional-parameters-in-msalnet"></a>Конкретные дополнительные параметры в MSAL.NET

#### <a name="withparentactivityorwindow"></a>СРодителейАктивностьОторОкно

UI важен, потому что он интерактивный. `AcquireTokenInteractive`имеет один конкретный дополнительный параметр, который может указать для платформ, поддерживающих его, родительский uI. При использовании в `.WithParentActivityOrWindow` настольном приложении, имеет другой тип, который зависит от платформы.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Примечания:

- На .NET Standard `object` `Activity` ожидается на `UIViewController` Android, `NSWindow` на iOS, на MAC, или `IWin32Window` на `IntPr` Windows.
- На Windows необходимо `AcquireTokenInteractive` вызвать из потока интерфейса, чтобы встроенный браузер получил соответствующий контекст синхронизации интерфейса. Не вызов из потока uI может привести к тому, что сообщения не будут перекачивать должным образом, а сценарии взаимоблокировки с помощью uI не перекачивают. Один из способов вызова библиотек подлинности Майкрософт (MSALs) из потока uI, если `Dispatcher` вы не в потоке uI уже является использование на WPF.
- Если вы используете WPF, чтобы получить окно из-под контроля `WindowInteropHelper.Handle` WPF, вы можете использовать класс. Затем звонок из управления WPF`this`():

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>СПромтом

`WithPrompt()`используется для управления интерактивностью с пользователем, указывая запрос.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Класс определяет следующие константы:

- ``SelectAccount``вынуждает STS представить диалоговую базу выбора учетной записи, содержащую учетные записи, для которых пользователь имеет сеанс. Эта опция полезна, когда разработчики приложений хотят, чтобы пользователи могли выбирать между различными идентификаторами. Эта опция приводит ``prompt=select_account`` к отправке MSAL поставщику идентификационных данных. Этот параметр выбирается по умолчанию. Он делает хорошую работу по обеспечению наилучшего опыта на основе имеющейся информации, таких как учетная запись и наличие сессии для пользователя. Не меняйте его, если у вас нет веских причин, чтобы сделать это.
- ``Consent``позволяет разработчику приложения заставить пользователя получить согласие, даже если согласие было получено ранее. В этом случае MSAL `prompt=consent` отправляет поставщику идентификационных данных. Эта опция может быть использована в некоторых приложениях, ориентированных на безопасность, где руководство организации требует, чтобы пользователю было представлено поле диалога согласия при каждом использовании приложения.
- ``ForceLogin``позволяет разработчику приложения заставить пользователя получить учетные данные службой, даже если эта подсказка может не понадобиться. Эта опция может быть полезна, чтобы позволить пользователю войти снова, если приобретение маркера не удается. В этом случае MSAL `prompt=login` отправляет поставщику идентификационных данных. Иногда он используется в приложениях, ориентированных на безопасность, где управление организации требует, чтобы пользователь повторно подписывался при каждом доступе к определенным частям приложения.
- ``Never``(только для .NET 4.5 и WinRT) пользователь не подскажет, а вместо этого попытается использовать файлcookieое печенье, хранящееся в скрытом встроенном веб-виде. Для получения дополнительной информации смотрите веб-представления в MSAL.NET. Использование этой опции может привести к сбою. В этом `AcquireTokenInteractive` случае выбрасывает исключение для уведомления о необходимости взаимодействия с uI. Вам нужно будет использовать `Prompt` другой параметр.
- ``NoPrompt``не будет отправлять запрос поставщику идентификационных данных. Эта опция полезна только для политики B2C по речевому отваге Функционального каталога Azure (Azure AD). Для получения дополнительной информации смотрите [специфику Azure AD B2C](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withextrascopetoconsent"></a>СЭкстраскопто-согласие

Этот модификатор используется в расширенном сценарии, где вы хотите, чтобы пользователь предварительно согласился на несколько ресурсов заранее, и вы не хотите использовать дополнительное согласие, которое обычно используется с MSAL.NET/the платформы идентификации Microsoft. Для получения дополнительной информации [см.](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>СCustomWebUi

Веб-интерфейс — это механизм для ввода браузера. Этот механизм может быть выделенным управлением UI WebBrowser или способом делегирования открытия браузера.
MSAL предоставляет веб-интерфейс реализации для большинства платформ, но Есть случаи, когда вы можете разместить браузер самостоятельно:

- Платформы, которые явно не охвачены MSAL, например, Blazor, Unity и Mono на настольных компьютерах.
- Вы хотите, чтобы uI протестировать приложение и использовать автоматизированный браузер, который можно использовать с Selenium.
- Браузер и приложение, запускаемые MSAL, находятся в отдельных процессах.

##### <a name="at-a-glance"></a>Вкратце

Для достижения этой цели, `start Url`вы даете MSAL , который должен быть отображается в браузере выбора, так что конечный пользователь может ввести элементы, такие как их имя пользователя.
После завершения проверки подлинности приложение должно быть `end Url`передано обратно в MSAL, который содержит код, предоставленный Azure AD.
Хозяин `end Url` всегда `redirectUri`. Чтобы `end Url`перехватить, сделайте одну из следующих вещей:

- Монитор браузер перенаправляет, пока не `redirect Url` ударил.
- Перенаправьте браузер на URL-адрес, который вы отслеживаете.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi является точкой размнозамости

`WithCustomWebUi`является точкой расширяемости, которую можно использовать для предоставления собственного uI в общедоступных клиентских приложениях. Вы также можете позволить пользователю пройти через /Авторизовать конечную точку поставщика идентификационных данных и позволить ему войти и дать согласие. MSAL.NET можете выкупить код проверки подлинности и получить токен. Например, он используется в Visual Studio, чтобы электроны приложений (например, Visual Studio Обратная связь) обеспечивают веб-взаимодействия, но оставить его MSAL.NET, чтобы сделать большую часть работы. Вы также можете использовать его, если вы хотите обеспечить автоматизацию uI. В публичных клиентских приложениях MSAL.NET использует стандарт Proof Key for Code Exchange (PKCE) для обеспечения соблюдения безопасности. Только MSAL.NET можете выкупить код. Для получения дополнительной информации, [см. RFC 7636 - Доказательство ключ для обмена кода OAuth государственных клиентов](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Использование WithCustomWebUi

Для `.WithCustomWebUI`использования, следуйте этим шагам.

  1. Реализуйте интерфейс `ICustomWebUi`. Для получения дополнительной информации, [см.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70) Реализация `AcquireAuthorizationCodeAsync`одного метода и примите URL-адрес кода авторизации, вычисленный MSAL.NET. Затем позвольте пользователю пройти через взаимодействие с поставщиком идентификационных данных и вернуть URL-адрес, с помощью которого поставщик идентификационных данных отозвал бы вашу реализацию вместе с кодом авторизации. Если у вас есть проблемы, ваша реализация должна бросить `MsalExtensionException` исключение, чтобы красиво сотрудничать с MSAL.
  2. В `AcquireTokenInteractive` своем вызове `.WithCustomUI()` используйте модификатор, передающий экземпляр пользовательского пользовательского иммунитета пользовательского или айму.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Примеры внедрения ICustomWebUi в автоматизацию тестирования: SeleniumWebUI

Команда MSAL.NET переписала тесты uI, чтобы использовать этот механизм разжижаемости. Если вы заинтересованы, посмотрите на класс [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) в MSAL.NET исходного кода.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Обеспечить большой опыт работы с SystemWebViewOptions

Из MSAL.NET 4.1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet), вы можете указать:

- URI, чтобы перейти к (`BrowserRedirectError`)`HtmlMessageError`или HTML фрагмент для отображения ( ) в случае регистрации или согласия ошибок в системе веб-браузера.
- URI, чтобы перейти к (`BrowserRedirectSuccess`)`HtmlMessageSuccess`или HTML фрагмент для отображения ( ) в случае успешного вхилья или согласия.
- Действие для запуска системного браузера. Собственную реализацию можно предоставить, установив делегата. `OpenBrowserAsync` Класс также обеспечивает реализацию по умолчанию `OpenWithEdgeBrowserAsync` `OpenWithChromeEdgeBrowserAsync` для двух браузеров: и для Microsoft Edge и [Microsoft Edge на Chromium](https://www.windowscentral.com/faq-edge-chromium), соответственно.

Чтобы использовать эту структуру, напишите что-то вроде следующего примера:

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Другие дополнительные параметры

Чтобы узнать больше обо всех `AcquireTokenInteractive`других дополнительных параметров, см. [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python не предоставляет интерактивный метод приобретения токенов напрямую. Вместо этого он требует, чтобы приложение отправило запрос на авторизацию в процессе реализации потока взаимодействия пользователя для получения кода авторизации. Этот код может быть `acquire_token_by_authorization_code` передан методу получения маркера.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])    

```

# <a name="macos"></a>[Macos](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>В MSAL для iOS и macOS

Objective-C.

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
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

Swift:

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```
---

## <a name="integrated-windows-authentication"></a>Встроенная проверка подлинности Windows

Чтобы войти в пользовательский домен на домене или в совместной машине Azure AD, используйте интегрированную аутентификацию Windows (IWA).

### <a name="constraints"></a>Ограничения

- Интегрированная аутентификация Windows может быть пригодна только для *пользователей-федеративное* пользователей, т.е. пользователей, созданных в Active Directory и поддерживаемых Azure AD. Пользователи, созданные непосредственно в Azure AD без поддержки Active Directory, известной как *управляемые* пользователи, не могут использовать этот поток аутентификации. Это ограничение не влияет на имя пользователя и поток паролей.
- IWA предназначен для приложений, написанных для платформ .NET Framework, .NET Core и Universal Windows Platform (UWP).
- IWA не обходит многофакторную аутентификацию (MFA). Если МИД настроен, IWA может выполнить неудачу, если требуется вызов MFA, поскольку МИД требует взаимодействия с пользователем.
  > [!NOTE]
  > Это один сложно. IWA не является интерактивным, но МИД требует интерактивности пользователя. Вы не контролируете, когда поставщик идентификационных данных запрашивает мида, чтобы быть выполнены, арендатор админ делает. По нашим наблюдениям, МИД требуется при входом из другой страны, когда вы не подключены через VPN к корпоративной сети, а иногда даже при подключении через VPN. Не ожидайте детерминированного набора правил. Azure AD использует ИИ для непрерывного изучения необходимости В МИД. Вернитесь к запросу пользователя, например интерактивной аутентификации или потоку кода устройства, если IWA не справляется.

- Полномочия, переданные в `PublicClientApplicationBuilder` должно быть:
  - Аренда формы, `https://login.microsoftonline.com/{tenant}/`где `tenant` либо GUID, который представляет идентификатор арендатора или домен, связанный с арендатором.
  - Для любой работы `https://login.microsoftonline.com/organizations/`и школьных счетов: .
  - Личные учетные записи Майкрософт не поддерживаются. Вы не можете использовать /общие или /потребителей арендаторов.

- Потому что интегрированная аутентификация Windows является молчаливым потоком:
  - Пользователь приложения должен был ранее дать согласие на использование приложения.
  - Или, как должен быть, админ-арендатор ранее дал согласие всем пользователям в арендаторе на использование приложения.
  - Другими словами:
    - Либо вы, будучи разработчиком, выбрали кнопку **Гранта** на портале Azure для себя.
    - Или, как админ-арендатор выбрал **грант/отзыв админа на кнопку «арендатор домена»** на вкладке **разрешений API** регистрации приложения. Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)
    - Или вы предоставили пользователям возможность дать согласие на приложение. Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent)
    - Или вы предоставили поставщику данных возможность дать согласие на подачу заявления. Для получения дополнительной [Admin consent](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant)информации, см.

- Этот поток включен для настольных компьютеров .NET, .NET Core и UWP приложений.

Для получения дополнительной информации о согласии [см.](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="learn-how-to-use-it"></a>Сведения об использовании

# <a name="net"></a>[.NET](#tab/dotnet)

В MSAL.NET необходимо использовать:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Обычно вам нужен только`scopes`один параметр (). В зависимости от способа настройки политики администратора Windows приложениям на вашем компьютере Windows может быть запрещено искать пользователя, вписавого в систему. В этом случае используйте `.WithUsername()`второй метод и передайте имя пользователя в качестве формата UPN, например, `joe@contoso.com`. На основе .NET Core доступна только перегрузка имени пользователя, поскольку платформа .NET Core не может запросить имя пользователя в ОС.

В следующем примере представлен самый актуальный случай, с объяснениями вида исключений, которые вы можете получить, и их смягчения.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

Список возможных модификаторов на AcquireTokenByIntegratedWindowsAuthentication можно узнать на [примере](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)AcquireTokenByIntegratedWindowsAuthentication, см.

# <a name="java"></a>[Java](#tab/java)

Этот экстракт из [образцов разработчиков MSAL Java.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Этот поток еще не поддерживается в MSAL Python.

# <a name="macos"></a>[Macos](#tab/macOS)

Этот поток не распространяется на MacOS.

---

## <a name="username-and-password"></a>Имя пользователя и пароль

Вы также можете приобрести токен, предоставив имя пользователя и пароль. Этот поток ограничен и не рекомендуется, но Есть еще использовать случаи, когда это необходимо.

### <a name="this-flow-isnt-recommended"></a>Этот поток не рекомендуется

Этот поток *не рекомендуется,* потому что наличие приложения просить пользователя для их пароль не является безопасным. Для получения дополнительной информации, см [Какое решение растущей проблемы паролей?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Предпочтительным потоком для приобретения токена, бесшумно на соединившихся компьютерах домена Windows, является [интегрированная аутентификация Windows.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication) Вы также можете использовать [поток кода устройства.](https://aka.ms/msal-net-device-code-flow)

> [!NOTE]
> Использование имени пользователя и пароля полезно в некоторых случаях, таких как сценарии DevOps. Но если вы хотите использовать имя пользователя и пароль в интерактивных сценариях, где вы предоставляете свой собственный пользовательский интерфейс, подумайте о том, как отойти от него. Используя имя пользователя и пароль, вы отказываетесь от ряда вещей:
>
> - Основные принципы современной идентичности. Пароль может быть воспроизведен и воспроизведен, поскольку общий секрет может быть перехвачен. Это несовместимо с безпарочкой.
> - Пользователи, которым необходимо сделать МИД, не могут войти в систему, потому что нет взаимодействия.
> - Пользователи не могут сделать один очный знак (SSO).

### <a name="constraints"></a>Ограничения

Следующие ограничения также применяются:

- Поток имени пользователя и паролей не совместим с условным доступом и многофакторной аутентификацией. Как следствие, если ваше приложение работает в адепрах Azure AD, где админ-арендатор требует многофакторной проверки подлинности, этот поток не используется. Многие организации делают это.
- Он работает только для работы и школьных счетов (не MSA).
- Поток доступен на рабочем столе .NET и .NET Core, но не на UWP.

### <a name="b2c-specifics"></a>Специфика B2C

Для получения дополнительной информации [см.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c)

### <a name="use-it"></a>Используйте его

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`содержит метод `AcquireTokenByUsernamePassword`.

В следующем примере представлен упрощенный случай.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

В следующем примере представлен самый актуальный случай, с объяснениями вида исключений, которые вы можете получить, и их смягчения.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Для получения дополнительной информации обо всех `AcquireTokenByUsernamePassword`модификаторах, которые могут быть применены к , см. [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

# <a name="java"></a>[Java](#tab/java)

Следующий экстракт из [образцов разработчиков MSAL Java.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Этот экстракт из [образцов разработчиков MSAL Python.](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macos"></a>[Macos](#tab/macOS)

Этот поток не поддерживается на MSAL для macOS.

---

## <a name="command-line-tool-without-a-web-browser"></a>Инструмент командной строки без веб-браузера

### <a name="device-code-flow"></a>Поток кода устройства

Если вы пишете инструмент командной строки, который не имеет веб-элементов управления, и вы не можете или не хотите использовать предыдущие потоки, вам необходимо использовать поток кода устройства.

Интерактивная аутентификация с Azure AD требует веб-браузера. Для получения дополнительной информации, см [Использование веб-браузеров](https://aka.ms/msal-net-uses-web-browser). Для проверки подлинности пользователей на устройствах или операционных системах, которые не предоставляют веб-браузер, поток кода устройства позволяет пользователю использовать другое устройство, такое как компьютер или мобильный телефон, чтобы войти в интерактивно. Используя поток кода устройства, приложение получает токены через двухступенчатый процесс, предназначенный для этих устройств или ОС. Примерами таких приложений являются приложения, которые работают на iOT или командных инструментах (CLI). Идея заключается в том, что:

1. Всякий раз, когда требуется аутентификация пользователя, приложение предоставляет код для пользователя. Пользователю предлагается использовать другое устройство, например, подключенный к Интернету `https://microsoft.com/devicelogin`смартфон, чтобы перейти на URL, например, . Затем пользователю предлагается ввести код. Это делается, веб-страница приводит пользователя через нормальный опыт проверки подлинности, который включает в себя запросы согласия и многофакторной аутентификации, если это необходимо.

2. После успешной проверки подлинности приложение командной строки получает необходимые токены через обратный канал и использует их для выполнения веб-вызовов API, в которых он нуждается.

### <a name="use-it"></a>Используйте его

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`содержит метод `AcquireTokenWithDeviceCode`под названием .

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Этот метод принимает в качестве параметров:

- Запрашивать `scopes` токен доступа.
- Обратный вызов, `DeviceCodeResult`который получает .

  ![Свойства DeviceCodeResult](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Следующий пример кода представляет собой наиболее актуальный случай, с объяснениями рода исключений, которые вы можете получить, и их смягчение.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }         
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }
    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
# <a name="java"></a>[Java](#tab/java)

Этот экстракт из [образцов разработчиков MSAL Java.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Этот экстракт из [образцов разработчиков MSAL Python.](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macos"></a>[Macos](#tab/macOS)

Этот поток не распространяется на MacOS.

---

## <a name="file-based-token-cache"></a>Кэш маркеров на основе файлов

В MSAL.NET по умолчанию предоставляется кэш маркеров в памяти.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>Сериализация настраивается в настольных приложениях Windows и веб-приложениях или веб-АБА

В случае с системой .NET и ядром .NET, если вы не сделаете ничего лишнего, кэш маркера в памяти длится в течение всего срока действия приложения. Чтобы понять, почему сериализация не предоставляется из коробки, помните, что настольные приложения MSAL .NET или .NET Core могут быть консольными или Windows-приложениями (которые будут иметь доступ к файловой системе), *а также* веб-приложениями или веб-AIS. Эти веб-приложения и веб-AI могут использовать некоторые конкретные механизмы кэша, такие как базы данных, распределенные кэши и кэши Redis. Для постоянного приложения кэша токенов на рабочем столе .NET или в ядро .NET необходимо настроить сериализацию.

Классы и интерфейсы, участвующие в сериализации кэша токенов, являются следующими типами:

- ``ITokenCache``, который определяет события, чтобы подписаться на запросы последовательного кэша токенов, и методы сериализации или десеризирования кэша в различных форматах (ADAL v3.0, MSAL 2.x и MSAL 3.x - ADAL v5.0).
- Обратный вызов ``TokenCacheCallback`` передается событиям, что позволяет обрабатывать операции сериализации. Они будут вызываться с аргументами типа ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs``предоставляет только ``ClientId`` приложение и ссылку на пользователя, для которого доступен токен.

  ![Диаграмма сериализации кэша токенов](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET создает кэши маркеров и предоставляет кэш `IToken` при вызове свойств `UserTokenCache` и `AppTokenCache` приложения. Вы не должны реализовать интерфейс самостоятельно. При реализации пользовательской сериализации для кэша маркеров в вашу зону ответственности входит следующее:
>
> - Реагируйте `BeforeAccess` `AfterAccess` и на события, или их аналог *Async.* Делегат`BeforeAccess` отвечает за десериализацию кэша. Делегат `AfterAccess` отвечает за сериализацию кэша.
> - Поймите, что часть этих событий хранит или загружает капли, которые передаются через аргумент события в любое хранилище, которое вы хотите.

Стратегии различны в зависимости от того, пишете ли вы сериализацию кэша токенов для приложения для публичного клиента, например на рабочем столе, или конфиденциального клиентского приложения, например веб-приложения или веб-aPI или приложения daemon.

С MSAL v2.x у вас есть несколько вариантов. Ваш выбор зависит от того, хотите ли вы сериализировать кэш только в формате MSAL.NET, который является единым кэшем формата, который является общим для MSAL, но и на всех платформах. Или также может потребоваться поддержка сериализации кэша маркеров [ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) v3.

Настройка сериализации кэша токенов для совместного использования состояния SSO между ADAL.NET 3.x, ADAL.NET 5.x и MSAL.NET объясняется в части образца [active-directory-dotnet-v1-to-v2.](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Простая сериализация кэша маркеров (только для MSAL)

Следующим примером является наивная реализация пользовательского сериализации кэша токенов для настольных приложений. Здесь кэш маркеров пользователя находится в файле в той же папке, что и приложение.

После создания приложения вы включите сериализацию, ``TokenCacheHelper.EnableSerialization()`` `UserTokenCache`позвонив и передав приложение.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Этот класс помощника выглядит следующим фрагментом кода:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Предварительный просмотр файлообменника качества кекса продукта для публичных клиентских приложений для настольных приложений, работающих на Windows, Mac и Linux, доступен в библиотеке [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) с открытым исходным кодом. Вы можете включить его в свои приложения из следующего пакета NuGet: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Отказ от ответственности: Библиотека Microsoft.Identity.Client.Extensions.Msal является расширением MSAL.NET. Классы в этих библиотеках могут внести свой путь в MSAL.NET в будущем, как есть или с нарушением изменений.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Двойной сериализация кэша токенов (MSAL unified cache - ADAL v3)

Возможно, необходимо реализовать сериализацию кэша токетов с форматом Единого кэша. Этот формат является общим для ADAL.NET 4,x и MSAL.NET 2,x, а также с другими MSALs того же поколения или старше, на той же платформе. Вдохновляйтесь следующим кодом:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

На этот раз класс помощника выглядит следующим кодом:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Вызов веб-API из настольного приложения](scenario-desktop-call-api.md)
