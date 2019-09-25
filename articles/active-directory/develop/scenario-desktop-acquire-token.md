---
title: Классическое приложение, вызывающее веб-API (получение маркера для приложения) — платформа Microsoft Identity
description: Узнайте, как создать классическое приложение, вызывающее веб-API (получение маркера для приложения |).
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8d46e873d48de5f7e507566b5af6095b9c4e1c
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268382"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>Классическое приложение, вызывающее веб-API — получение маркера

После сборки `IPublicClientApplication`вы будете использовать его для получения маркера, который затем будет использоваться для вызова веб-API.

## <a name="recommended-pattern"></a>Рекомендуемый шаблон

Веб-API определяется его `scopes`свойством. Независимо от того, какие возможности вы задаете в приложении, шаблон, который вы хотите использовать:

- Систематически попытайтесь получить маркер из кэша маркеров, вызвав метод`AcquireTokenSilent`
- Если этот вызов завершается неудачей, используйте `AcquireToken` последовательность, которую вы хотите использовать ( `AcquireTokenXX`представленную).

### <a name="in-msalnet"></a>В MSAL.NET

```CSharp
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
SWIFT

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

Ниже приведено подробное описание различных способов получения маркеров в классическом приложении.

## <a name="acquiring-a-token-interactively"></a>Интерактивное получение маркера

В следующем примере показан минимальный код для интерактивного получения маркера для чтения профиля пользователя с Microsoft Graph.

### <a name="in-msalnet"></a>В MSAL.NET

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

SWIFT

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

### <a name="mandatory-parameters"></a>Обязательные параметры

`AcquireTokenInteractive`имеет только один обязательный ``scopes``параметр, который содержит перечисление строк, определяющих области, для которых требуется токен. Если маркер предназначен для Microsoft Graph, необходимые области можно найти в справочнике по API для каждого API Microsoft Graph в разделе "разрешения". Например, чтобы получить [список контактов пользователя](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), необходимо будет использовать область "пользователь. чтение", "Contacts. Read". См. также [Microsoft Graph ссылки на разрешения](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

В Android необходимо также указать родительское действие (с помощью `.WithParentActivityOrWindow`, см. ниже), чтобы токен был возвращен к родительскому действию после взаимодействия. Если не указать его, при вызове `.ExecuteAsync()`будет создано исключение.

### <a name="specific-optional-parameters-in-msalnet"></a>Определенные необязательные параметры в MSAL.NET

#### <a name="withparentactivityorwindow"></a>виспарентактивитйорвиндов

В интерактивном режиме UI очень важен. `AcquireTokenInteractive`имеет один конкретный необязательный параметр, позволяющий указать для платформ, поддерживающих его, родительский пользовательский интерфейс. При использовании в классическом приложении `.WithParentActivityOrWindow` имеет другой тип в зависимости от платформы:

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Примечания:

- На `object` .NET Standard ожидаемый результат `Activity` — в Android, `UIViewController` в `IWin32Window` iOS, `NSWindow` на Mac, а также в или `IntPr` в Windows.
- В Windows необходимо вызвать `AcquireTokenInteractive` из потока пользовательского интерфейса, чтобы встроенный браузер получит соответствующий контекст синхронизации пользовательского интерфейса.  Отсутствие вызова из потока пользовательского интерфейса может привести к тому, что сообщения не будут передаваться должным образом или в сценарии взаимоблокировки с пользовательским интерфейсом. Один из способов вызова MSAL из потока пользовательского интерфейса, если вы не работаете в потоке пользовательского интерфейса, уже `Dispatcher` использует в WPF.
- Если вы используете WPF для получения окна из элемента управления WPF, можно использовать `WindowInteropHelper.Handle` класс. Затем вызов из элемента управления WPF (`this`):
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>виспромпт

`WithPrompt()`используется для управления интерактивностью пользователя путем указания запроса

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Класс определяет следующие константы:

- ``SelectAccount``: заставит STS предоставить диалоговое окно выбора учетной записи, содержащее учетные записи, для которых у пользователя есть сеанс. Этот параметр полезен в тех случаях, когда разработчики приложений хотят позволить пользователю выбирать из разных удостоверений. Этот параметр позволяет MSAL отправить ``prompt=select_account`` поставщику удостоверений. Этот параметр используется по умолчанию, и он обеспечивает оптимальную работу на основе доступных сведений (учетной записи, присутствия сеанса для пользователя и т. д.). ...). Не изменяйте его, если у вас нет веских причин.
- ``Consent``: позволяет разработчику приложения принудительно запрашивать согласие пользователя, даже если было предоставлено согласие. В этом случае MSAL отправляет `prompt=consent` поставщику удостоверений. Этот параметр можно использовать в некоторых приложениях безопасности, в которых Управление организацией требует, чтобы пользователь предвидит диалоговое окно согласия при каждом использовании приложения.
- ``ForceLogin``: позволяет разработчику приложения получать запрос учетных данных от службы, даже если этот пользователь не нужен. Этот параметр может быть полезен в случае сбоя при получении маркера, чтобы пользователь мог повторно выполнить вход. В этом случае MSAL отправляет `prompt=login` поставщику удостоверений. Опять же, мы видели, что он использовался в некоторых приложениях, ориентированных на безопасность, где управление Организацией требует, чтобы пользователь повторно вошел в систему при каждом доступе к определенным частям приложения.
- ``Never``(только для .NET 4,5 и WinRT) не запрашивает пользователя, а вместо этого будет пытаться использовать файл cookie, хранящийся в скрытом внедренном веб-представлении (см. ниже: Веб-представления в MSAL.NET). При использовании этого параметра может произойти сбой, и в `AcquireTokenInteractive` этом случае будет создано исключение, уведомляющее о необходимости взаимодействия пользовательского интерфейса, и необходимо использовать другой `Prompt` параметр.
- ``NoPrompt``. Не отправляет запрос поставщику удостоверений. Этот параметр полезен только для Azure AD B2C редактирования политик профилей (см. раздел [особенности B2C](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>висекстраскопетоконсент

Этот модификатор используется в расширенном сценарии, где пользователь должен заранее согласиться с несколькими ресурсами (и вы не хотите использовать добавочное согласие, которое обычно используется с MSAL.NET или платформой идентификации Майкрософт). Дополнительные сведения см. в разделе [как получить согласие пользователя на получение нескольких ресурсов](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>вискустомвебуи

Веб-интерфейс — это механизм для вызова браузера. Этот механизм может быть выделенным элементом управления WebBrowser пользовательского интерфейса или способом делегирования открытия браузера.
MSAL предоставляет реализации пользовательского веб-интерфейса для большинства платформ, но все еще бывают случаи, когда может потребоваться самостоятельное размещение браузера: 

- платформы, которые не были явно охвачены MSAL, например Блазор, Unity, Mono на настольном компьютере
- необходимо протестировать приложение пользовательского интерфейса и использовать автоматизированный браузер, который можно использовать с Selenium 
- браузер и приложение, в котором выполняется MSAL, находятся в разных процессах

##### <a name="at-a-glance"></a>Кратко

Для этого вы MSAL a `start Url`, который должен отображаться в выбранном браузере, чтобы конечный пользователь мог ввести имя пользователя и т. д. После завершения проверки подлинности приложение потребуется передать обратно в MSAL `end Url`, который содержит код, предоставленный Azure AD.
Узел объекта `end Url` всегда имеет значение `redirectUri`. Чтобы перехватить, `end Url` можно выполнить следующие действия. 

- Отслеживайте перенаправления браузеров до `redirect Url` попадания или
- Перенаправление браузера на URL-адрес, который вы отслеживаете

##### <a name="withcustomwebui-is-an-extensibility-point"></a>Вискустомвебуи — это точка расширяемости

`WithCustomWebUi`— Это точка расширяемости, которая позволяет предоставить собственный пользовательский интерфейс в общедоступных клиентских приложениях и позволить пользователю проходить через конечную точку/Authorize поставщика удостоверений и предоставить им возможность входа и предоставления согласия. MSAL.NET может, затем активировать код проверки подлинности и получить маркер. Этот экземпляр используется в Visual Studio для работы с электронными сообщениями (например, Отзывы о экземпляре VS), но не MSAL.NET для выполнения большей части работы. Его также можно использовать, если требуется обеспечить автоматизацию пользовательского интерфейса. В общедоступных клиентских приложениях MSAL.NET использует стандарт PKCE ([RFC 7636 — ключ подтверждения для обмена кодом](https://tools.ietf.org/html/rfc7636)в общедоступных клиентах OAuth), чтобы обеспечить соблюдение безопасности. Только MSAL.NET может активировать код.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>Как использовать Вискустомвебуи

Чтобы использовать `.WithCustomWebUI`, необходимо:
  
  1. Реализуйте интерфейс (см. [здесь.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70) `ICustomWebUi` В основном необходимо реализовать один метод `AcquireAuthorizationCodeAsync` , который принимает URL-адрес кода авторизации (рассчитанный на MSAL.NET), позволяя пользователю проходить через взаимодействие с поставщиком удостоверений, а затем возвращая URL-адрес, по которому поставщик удостоверений вызвала обратную реализацию (включая код авторизации). Если у вас возникли проблемы, ваша реализация должна `MsalExtensionException` вызвать исключение, чтобы хорошо взаимодействовать с MSAL.
  2. В вызове можно использовать `.WithCustomUI()` модификатор, передающий экземпляр пользовательского пользовательского веб-интерфейса. `AcquireTokenInteractive`

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>Примеры реализации Икустомвебуи в автоматизации тестирования — Селениумвебуи

Команда MSAL.NET перезаписала наши тесты пользовательского интерфейса, чтобы использовать этот механизм расширяемости. Если вас интересует, вы можете взглянуть на класс [селениумвебуи](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) в исходном коде MSAL.NET

##### <a name="providing-a-great-experience-with-systemwebviewoptions"></a>Предоставление отличного опыта работы с Системвебвиевоптионс

Из MSAL.NET 4,1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet) позволяет указать:

- универсальный код ресурса (URI)`BrowserRedirectError`для перехода к () или отображаемый фрагмент`HtmlMessageError`HTML () в случае ошибок входа или согласия в системном веб-браузере
- универсальный код ресурса (URI)`BrowserRedirectSuccess`для перехода к () или фрагмент HTML для`HtmlMessageSuccess`вывода () в случае успешного входа или согласия.
- действие, выполняемое для запуска браузера системы. Для этого можно предоставить собственную реализацию, задав `OpenBrowserAsync` делегат. Класс также предоставляет реализацию по умолчанию для двух браузеров `OpenWithEdgeBrowserAsync` : `OpenWithChromeEdgeBrowserAsync`и, соответственно для Microsoft ребр и [Microsoft ребро в Chromium](https://www.windowscentral.com/faq-edge-chromium).

Чтобы использовать эту структуру, можно написать нечто вроде следующего:

```CSharp
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

#### <a name="other-optional-parameters"></a>Другие необязательные параметры

Дополнительные сведения о других необязательных параметрах `AcquireTokenInteractive` см. в справочной документации по [аккуиретокенинтерактивепараметербуилдер](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods) .

## <a name="integrated-windows-authentication"></a>Встроенная проверка подлинности Windows

Если вы хотите войти в систему пользователя домена на компьютере, присоединенном к Azure AD, необходимо использовать:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>Ограничения

- Аккуиретокенбинтегратедвиндовсаус (IWA) можно использовать только для **федеративных** пользователей, то есть пользователей, созданных в Active Directory и поддерживаемых Azure Active Directory. Пользователи, созданные непосредственно в AAD без резервного **копирования пользователей AD** , не могут использовать этот поток проверки подлинности. Это ограничение не влияет на поток имени пользователя и пароля.
- IWA предназначен для приложений, написанных для платформ .NET Framework, .NET Core и UWP
- IWA не обходит MFA (многофакторная проверка подлинности). Если настроено MFA, IWA может завершиться ошибкой, если требуется запрос MFA, так как для MFA требуется вмешательство пользователя.
  > [!NOTE]
  > Это очень просто. IWA не является интерактивным, но MFA требует взаимодействия с пользователем. Вы не контролируете, когда поставщик удостоверений запрашивает выполнение MFA, а администратор клиента выполняет. На основе наших наблюдений MFA требуется при входе из другой страны, если он не подключен через VPN к корпоративной сети, а иногда даже при подключении через VPN. Не следует рассчитывать на детерминированный набор правил, Azure Active Directory использует AI для непрерывного изучения необходимости MFA. Если IWA завершается неудачей, следует выполнить откат к приглашению пользователя (Интерактивная проверка подлинности или поток кода устройства).

- Центр, переданный в `PublicClientApplicationBuilder` , должен быть следующим:
  - Клиент-ED (в форме `https://login.microsoftonline.com/{tenant}/` , где `tenant` — это идентификатор GUID, представляющий идентификатор клиента или домен, связанный с клиентом.
  - для всех рабочих и учебных учетных`https://login.microsoftonline.com/organizations/`записей ()
  - Личные учетные записи Майкрософт не поддерживаются (вы не можете использовать клиенты "/Common" или/консумерс).

- Поскольку встроенная проверка подлинности Windows является продвижением в автоматическом режиме:
  - для использования приложения пользователь приложения должен быть предварительно отправлен.
  - или администратор клиента должен быть ранее передан всем пользователям в клиенте для использования приложения.
  - Другими словами:
    - Вы как разработчик нажимаете кнопку **предоставить** на портал Azure,
    - или администратор клиента нажал кнопку **GRANT/REVOKE Admin согласия администратора для {клиент домен}** на вкладке **разрешения API** для регистрации приложения (см. раздел [Добавление разрешений для доступа к веб-API](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)).
    - или вы указали способ предоставления пользователям согласия на приложение (см. раздел [запрос согласия отдельного пользователя](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent)).
    - или вы указали способ предоставления администратору клиента согласия для приложения (см. раздел [согласие администратора](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant)).

- Этот поток включен для приложений .NET для настольных систем, .NET Core и Windows Universal (UWP). В .NET Core доступно только перегрузка, принимающая имя пользователя, так как платформа .NET Core не может запросить имя пользователя для операционной системы.
  
Дополнительные сведения о согласии см. в разделе [разрешения и согласие платформы Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent) .

### <a name="how-to-use-it"></a>Использование

Обычно требуется только один параметр (`scopes`). Однако в зависимости от способа настройки политик администратором Windows может быть возможным, чтобы приложения на компьютере Windows не могли выполнять поиск вошедшего в систему пользователя. В этом случае используйте второй метод `.WithUsername()` и передайте имя пользователя, выполнившего вход, в качестве имени UPN-. `joe@contoso.com`

В следующем примере представлен наиболее актуальный вариант с объяснениями типов исключений, которые вы можете получить, и их устранение.

```CSharp
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

Список возможных модификаторов в Аккуиретокенбинтегратедвиндовсаусентикатион см. в разделе [аккуиретокенбинтегратедвиндовсауспараметербуилдер](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>Имя пользователя и пароль

Можно также получить маркер, указав имя пользователя и пароль. Этот поток ограничен и не рекомендуется, но по-прежнему используются случаи, когда это необходимо.

### <a name="this-flow-isnt-recommended"></a>Этот поток не рекомендуется

Этот поток **не рекомендуется** , так как приложение, запрашивающее у пользователя пароль, не является безопасным. Дополнительные сведения об этой проблеме см. в [этой статье](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Предпочтительным потоком для автоматического получения маркера на компьютерах, присоединенных к домену Windows, является [Встроенная проверка подлинности Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). В противном случае можно также использовать [поток кода устройства](https://aka.ms/msal-net-device-code-flow)

> [!NOTE] 
> Хотя это полезно в некоторых случаях (DevOps сценариях), если вы хотите использовать имя пользователя и пароль в интерактивных сценариях, где вы предоставляете пользовательский интерфейс ОНВ, вам следует подумать о том, как их перемещать. Используя имя пользователя и пароль, вы предоставляете ряд вещей:
>
> - Основные клиенты современного удостоверения: пароль передается, а затем воспроизводится. Так как мы предоставляем эту концепцию общего секрета, который можно перехватить.
> Это несовместимо с паролем.
> - Пользователи, которым требуется выполнить MFA, не смогут войти в систему (так как взаимодействие отсутствует).
> - Пользователи не смогут выполнять единый вход.

### <a name="constraints"></a>Ограничения

Также применяются следующие ограничения.

- Поток имени пользователя и пароля несовместим с условным доступом и многофакторной проверкой подлинности: Как следствие, если приложение выполняется в клиенте Azure AD, где администратор клиента требует многофакторную проверку подлинности, вы не сможете использовать этот поток. Многие организации делают это.
- Он работает только для рабочих и учебных учетных записей (не MSA).
- Поток доступен на платформе .NET Desktop и .NET Core, но не в UWP

### <a name="b2c-specifics"></a>Особенности B2C

[Дополнительные сведения об использовании ропк с B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>Как его использовать?

`IPublicClientApplication`содержит метод`AcquireTokenByUsernamePassword`

В следующем примере представлен упрощенный вариант.

```CSharp
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

В следующем примере представлен наиболее актуальный вариант с объяснениями типов исключений, которые вы можете получить, и их устранение.

```CSharp
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

Дополнительные сведения обо всех модификаторах, которые могут быть применены `AcquireTokenByUsernamePassword`к, см. в разделе [аккуиретокенбюсернамепассвордпараметербуилдер](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>Программа командной строки (без веб-браузера)

### <a name="device-code-flow-why-and-how"></a>Почему же поток кода устройства? и как?

Если вы создаете программу командной строки (которая не содержит веб-элементы управления) и не можете или не хотите использовать предыдущие последовательности, необходимо использовать `AcquireTokenWithDeviceCode`.

Для интерактивной проверки подлинности в Azure AD требуется веб-браузер (Дополнительные сведения см. в разделе [Использование веб-браузеров](https://aka.ms/msal-net-uses-web-browser)). Однако для проверки подлинности пользователей на устройствах или операционных системах, которые не предоставляют веб-браузер, поток кода устройства позволяет пользователю использовать другое устройство (например, другой компьютер или мобильный телефон) для входа в интерактивном режиме. С помощью потока кода устройства приложение получает маркеры через двухэтапный процесс, специально предназначенный для этих устройств и ОС. Примерами таких приложений являются приложения, работающие в iOT, или средства командной строки (CLI). Идея состоит в том, что:

1. Всякий раз, когда требуется проверка подлинности пользователя, приложение предоставляет код и запрашивает у пользователя использование другого устройства (например, смартфона, подключенного к Интернету) для перехода по `https://microsoft.com/devicelogin`URL-адресу (например,), где пользователю будет предложено ввести код. Это сделало то, что веб-страница сделает пользователя обычным процессом проверки подлинности, включая запросы согласия и многофакторную проверку подлинности, если это необходимо.

2. После успешной проверки подлинности приложение командной строки получит необходимые токены через канал обратного вызова и будет использовать его для выполнения необходимых им вызовов веб-API.

### <a name="code"></a>Код

`IPublicClientApplication`содержит метод с именем`AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Этот метод принимает в качестве параметров:

- , `scopes` Чтобы запросить маркер доступа для
- Обратный вызов, который получит`DeviceCodeResult`

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

В следующем образце кода представлен наиболее актуальный вариант с объяснением типа исключений, которые вы можете получить, и их устранением.

```CSharp
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

## <a name="file-based-token-cache"></a>Кэш маркера на основе файлов

В MSAL.NET по умолчанию предоставляется кэш маркеров в памяти.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>Сериализация настраивается в классических приложениях Windows, а веб-приложениях и веб-API.

В случае .NET Framework и .NET Core, если не выполнить никаких дополнительных действий, кэш маркеров в памяти продолжается в течение всего времени работы приложения. Чтобы понять, почему сериализация не предоставлялась, помните, что приложения MSAL .NET Desktop/Core могут быть консольными и приложениями Windows (которые могут иметь доступ к файловой системе), **но также** веб-приложениям или веб-API. Эти веб-приложения и веб-API могут использовать определенные механизмы кэширования, такие как базы данных, распределенные кэши, кэши Redis и т. д. Чтобы иметь постоянное приложение кэша маркеров на рабочем столе или ядре .NET, необходимо настроить сериализацию.

Классы и интерфейсы, задействованные в сериализации кэша маркеров, имеют следующие типы:

- ``ITokenCache``, определяющий события для подписки на запросы сериализации кэша маркеров, а также методы сериализации или десериализации кэша в различных форматах (ADAL v 3.0, MSAL 2. x и MSAL 3. x = ADAL v 5.0)
- Обратный вызов ``TokenCacheCallback`` передается событиям, что позволяет обрабатывать операции сериализации. они будут вызываться с аргументами типа ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs``предоставляет ``ClientId`` только приложение и ссылку на пользователя, для которого доступен маркер.

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET создает кэши маркеров и предоставляет кэш `IToken` при вызове свойств `UserTokenCache` и `AppTokenCache` приложения. Вы не должны реализовывать интерфейс самостоятельно. При реализации пользовательской сериализации для кэша маркеров в вашу зону ответственности входит следующее:
>
> - Реагирование `BeforeAccess` на `AfterAccess` события и (или их *асинхронное* аналогия). Делегат отвечает за десериализацию кэша, в `AfterAccess` то время как он отвечает за сериализацию кэша.`BeforeAccess`
> - Часть этих событий сохраняют или загружают большие двоичные объекты, которые передаются через аргумент события в нужное хранилище.

Стратегии различаются в зависимости от того, пишется ли сериализация кэша маркеров для общедоступного клиентского приложения или конфиденциального клиентского приложения (веб-приложение или веб-интерфейс API, управляющее приложение).

Так как MSAL v2. x у вас есть несколько вариантов, в зависимости от того, нужно ли сериализовать кэш только в формате MSAL.NET (кэш универсального формата, который является общим для MSAL, но также на разных платформах) или если также требуется поддержка кэша [устаревших](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) маркеров. Сериализация ADAL v3.

Настройка сериализации кэша маркеров для совместного использования состояния единого входа между ADAL.NET 3. x, ADAL.NET 5. x и MSAL.NET описывается в следующем примере: [Active-Directory-DotNet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) .

### <a name="simple-token-cache-serialization-msal-only"></a>Простая сериализация кэша маркеров (только для MSAL)

Ниже приведен пример основной реализации пользовательской сериализации кэша маркеров для классических приложений. Здесь кэш пользовательского маркера находится в файле в той же папке, что и приложение.

После сборки приложения можно включить сериализацию, вызвав ``TokenCacheHelper.EnableSerialization()`` передачу приложения.`UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Этот вспомогательный класс выглядит как следующий фрагмент кода:

```CSharp
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

Предварительная версия кэша маркеров качества продукта для общедоступных клиентских приложений (для настольных приложений, работающих под управлением Windows, Mac и Linux) доступна в библиотеке [Microsoft. Identity. Client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) с открытым исходным кодом. Это решение можно включить в приложение из следующего пакета NuGet: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Заявление об отказе. Библиотека Microsoft. Identity. Client. Extensions. Msal является расширением для MSAL.NET. Классы в этих библиотеках могут стать MSAL.NET в будущем, как если бы это было, так и с критическими изменениями.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Сериализация кэша двойной лексемы (единый кэш MSAL + ADAL v3)

Если вы хотите реализовать сериализацию кэша маркеров как с единым форматом кэша (общим для ADAL.NET 4. x и MSAL.NET 2. x), так и с другими Мсалс того же поколения или более старой версии, на той же платформе), вы можете получить их в следующем коде. :

```CSharp
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

На этот раз вспомогательный класс выглядит следующим образом:

```CSharp
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

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Вызов веб-API из классического приложения](scenario-desktop-call-api.md)
