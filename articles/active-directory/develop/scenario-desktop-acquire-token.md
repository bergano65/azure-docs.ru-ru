---
title: Классическое приложение, вызывающее веб-интерфейсы API (получение токена для приложения) — платформе Microsoft identity
description: Узнайте, как создание классического приложения, которое вызывает веб-API (получение токена для приложения |)
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d84801d6368bcc29f08145f190c2a07c64050ced
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795086"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>Классическое приложение, вызывающее веб-интерфейсы API — получение маркера

После создания вы `IPublicClientApplication`, оно будет использоваться для получения маркера, который затем используется для вызова веб-API.

## <a name="recommended-pattern"></a>Рекомендуемый шаблон

Веб-API, определяется его `scopes`. Любое взаимодействие с вами в приложение, такое шаблон, который необходимо использовать.

- Систематически при попытке получения маркера из кэша маркера путем вызова `AcquireTokenSilent`
- Если этот вызов завершается неудачно, используйте `AcquireToken` поток, который вы хотите использовать (представленный здесь `AcquireTokenXX`)

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

Вот теперь подробные сведения о различных способах получения маркеров в приложении для настольных систем

## <a name="acquiring-a-token-interactively"></a>Получение токена в интерактивном режиме

Следующий пример показывает минимальный код для получения маркера в интерактивном режиме для чтения профиля пользователя с помощью Microsoft Graph.

```CSharp
string[] scopes = new string["user.read"];
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

`AcquireTokenInteractive` имеет только один обязательный параметр ``scopes``, который содержит перечисление строк, которые определяют области, для которых маркер является обязательным. Если токен для Microsoft Graph, требуется областей можно найти в справочник по api для каждого API Microsoft graph в разделе «Разрешения». Например, чтобы [список контактов пользователя](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), потребуется использовать область «User.Read», «Contacts.Read». См. также [Справочник по разрешениям Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

В Android, необходимо также указать родительское действие (с помощью `.WithParentActivityOrWindow`, см. ниже), чтобы маркер возвращается этому действию родительского после взаимодействия. Если он не указан, будет создано исключение, при вызове `.ExecuteAsync()`.

### <a name="specific-optional-parameters"></a>Определенные необязательные параметры

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Интерактивный, пользовательский Интерфейс — важные. `AcquireTokenInteractive` имеет один конкретных необязательный параметр, чтобы указать, для платформ, которые поддерживают включения родительского пользовательского интерфейса. При использовании в приложении для настольных систем `.WithParentActivityOrWindow` имеет другой тип зависимости от платформы:

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

- В .NET Standard, ожидаемый `object` является `Activity` в Android `UIViewController` на iOS, `NSWindow` на компьютере MAC и `IWin32Window` или `IntPr` на Windows.
- В Windows, необходимо вызвать `AcquireTokenInteractive` из пользовательского интерфейса потока, чтобы внедренный обозреватель получает соответствующий контекст синхронизации пользовательского интерфейса.  Не вызов из потока пользовательского интерфейса может вызвать сообщения не pump должным образом и/или принимать участие во взаимоблокировке сценариев с помощью пользовательского интерфейса. Один из способов вызова MSAL из потока пользовательского интерфейса, если вы не в потоке пользовательского интерфейса уже является использование `Dispatcher` на основе WPF.
- Если вы используете WPF, для получения окна элемента управления WPF, можно использовать `WindowInteropHelper.Handle` класса. Он затем из элемента управления WPF (`this`):
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` используется для контроля взаимодействия с пользователем, указав в строке

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Этот класс определяет следующие константы:

- ``SelectAccount``: приведет к STS для предоставления диалоговое окно выбора учетной записи, содержащей учетные записи, для которых у пользователя запущен сеанс. Этот параметр полезен в тех случаях, когда разработчики приложений требуется разрешить пользователям выбирать среди разных удостоверений. Этот вариант обеспечивает MSAL для отправки ``prompt=select_account`` на поставщика удостоверений. Этот параметр используется по умолчанию, и он выполняет хорошие предоставления наиболее удобной работы на основе доступных сведений (учетная запись, наличие сеанс для пользователя и т. д. ...). Не меняли его при отсутствии действительно необходимо.
- ``Consent``: позволяет приложению согласие запрашиваться разработчика, чтобы заставить пользователя, даже если перед предоставления согласия. В этом случае отправляет MSAL `prompt=consent` на поставщика удостоверений. Этот параметр может использоваться в некоторых приложениях безопасности с фокусом ввода, где руководства организации требует, что пользователь получает диалоговом окне согласия каждый раз, когда используется это приложение.
- ``ForceLogin``: позволяет разработчику приложения пользователь должен запрашиваться учетные данные, службой, даже если этот запрос пользователя не требуется. Этот параметр может быть полезным при сбое получения маркера, чтобы позволить пользователю повторно входа. В этом случае отправляет MSAL `prompt=login` на поставщика удостоверений. Опять же мы видели, она используется в некоторых приложениях безопасности с фокусом ввода, где руководства организации требует, что пользователь relogs в каждый раз, при доступе к определенной части приложения.
- ``Never`` (для .NET 4.5 и только WinRT) не будет запрашивать пользователя, но вместо этого будет пытаться использовать куки-файл хранится в представлении скрытый встроенный веб (см. ниже: Веб-представления в MSAL.NET). С помощью этого параметра может завершиться ошибкой и в этом случае `AcquireTokenInteractive` приведет к возникновению исключения для уведомления требуется взаимодействие с пользовательским Интерфейсом, что вам нужно будет использовать другой `Prompt` параметра.
- ``NoPrompt``. Не будет отправлять запросы к поставщику удостоверений. Этот параметр полезен только для Azure AD B2C изменить профиль политики (см. в разделе [особенности B2C](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Этот модификатор используется в расширенном сценарии место пользователю предварительно согласие на наращивание ресурсов выполняется несколько ресурсов (и не хотите использовать добавочное согласие, который обычно используется с помощью MSAL.NET и версии 2.0 платформы Microsoft identity). Дополнительные сведения см. [руководства: получить согласие заранее для нескольких ресурсов пользователя](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi — это точка расширяемости

`WithCustomWebUi` — Это точка расширяемости, позволяющий предоставить собственный пользовательский Интерфейс в общедоступных клиентских приложений и позволяют проходить через конечной точке/Authorize поставщика удостоверений и позволяют им вход и согласие. MSAL.NET можно, затем использовать код проверки подлинности и получить маркер. Для экземпляра он используется в Visual Studio требуется электроны приложений (например отзывов VS) обеспечивают взаимодействие web, но оставить ее для MSAL.NET проводить большую часть работы. Можно также использовать его, если вы хотите предоставить модели автоматизации пользовательского интерфейса. В общедоступных клиентских приложений, MSAL.NET использует стандартную PKCE ([7636 RFC - ключ подтверждения для кода Exchange с общедоступных клиентов OAuth](https://tools.ietf.org/html/rfc7636)) чтобы убедиться, что учитывается безопасности: Только MSAL.NET можно активировать код.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>Как использовать WithCustomWebUi

Чтобы использовать `.WithCustomWebUI`, вам потребуется:
  
  1. Реализуйте `ICustomWebUi` интерфейса (см. в разделе [здесь](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). По сути необходимо реализовать один метод `AcquireAuthorizationCodeAsync` принимает код авторизации URL-адрес, (вычисляется по MSAL.NET), проходят через взаимодействие с поставщиком удостоверений, чтобы пользователь, а затем возвращения резервного URL-адрес, по которому будет поставщика удостоверений вызвали реализации обратно (включая код авторизации). Если у вас есть проблемы, необходимо вызвать исключение реализации `MsalExtensionException` исключение хорошо взаимодействовать с помощью MSAL.
  2. В вашей `AcquireTokenInteractive` вызова, можно использовать `.WithCustomUI()` модификатор, передавая экземпляр настраиваемого веб-интерфейса

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>Примеры реализации ICustomWebUi в автоматизации тестирования - SeleniumWebUI

Команда MSAL.NET были переписаны наших тестов пользовательского интерфейса, а затем использовать этот механизм расширяемости. В случае, если вы хотите вы можете взглянуть на [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) класс в исходном коде MSAL.NET

#### <a name="other-optional-parameters"></a>Другие необязательные параметры

Дополнительные сведения о все другие необязательные параметры для `AcquireTokenInteractive` из справочная документация по [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="integrated-windows-authentication"></a>Встроенная проверка подлинности Windows

Если требуется выполнить вход в домен пользователь домена или Azure AD к машине, присоединенной, необходимо использовать:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>Ограничения

- AcquireTokenByIntegratedWindowsAuth (IWA) используется только для **федеративные** могут только пользователи, которые, пользователей, созданные в Active Directory и на базе Azure Active Directory. Пользователи, созданные непосредственно в Azure Active Directory, без резервного AD - **управляемых** пользователи — нельзя использовать этот поток проверки подлинности. Это ограничение не влияет на поток имя пользователя и пароль.
- IWA предназначен для приложений, написанных для платформы .NET Framework, .NET Core и универсальной платформы Windows платформ
- IWA не обходить MFA (многофакторной проверки подлинности). Если настройки многофакторной проверки Подлинности IWA Если может произойти сбой запрос MFA является обязательным, поскольку многофакторной проверки Подлинности требуется взаимодействие с пользователем.
  > [!NOTE]
  > Такой – непростая задача. Неинтерактивный режим IWA, но 2FA требует интерактивное взаимодействие с пользователем. Не требуется контролировать, когда поставщик удостоверений запрашивает 2FA должна выполнять, выполняет администратора клиента. Из наших наблюдений 2FA является обязательным при входе из другой страны, если не подключено через VPN-Подключение к корпоративной сети, а иногда даже в том случае, при подключении через VPN. Не ожидайте, что детерминированным набор правил, Azure Active Directory использует AI постоянно дополнительные Если 2FA не требуется. При сбое IWA следует откат к пользователю запрос (интерактивной проверки подлинности или устройство потока кода).

- Переданный полномочия `PublicClientApplicationBuilder` должен быть:
  - Эд клиента (в формате `https://login.microsoftonline.com/{tenant}/` где `tenant` — это guid, представляющий идентификатор клиента или домен, связанный с клиентом.
  - для любой рабочих и учебных учетных записей (`https://login.microsoftonline.com/organizations/`)

  > Личные учетные записи Майкрософт не поддерживаются (нельзя использовать "/ Common" или /consumers клиентов)

- Так как встроенная проверка подлинности Windows приведена silent.
  - пользователь приложения должен ранее свое согласие на использование приложения
  - или администратор клиента должен ранее согласились всех пользователей в клиенте, чтобы использовать приложение.
  - Другими словами:
    - либо вы как разработчик быть нажаты **Grant** кнопку на портале Azure для себя
    - или администратор клиента нажал **согласия администратора Grant/revoke для {домен}** кнопку в **разрешения API** вкладка регистрации для приложения (см. в разделе [добавьте разрешения для веб-доступа к API](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis))
    - или вы предоставили пользователям предоставить согласие для приложения (см. в разделе [запрос на получение согласия пользователя](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent))
    - или вы предоставили позволяет администратору клиентов предоставить согласие для приложения (см. в разделе [согласия администратора](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant))

- Этот поток предусмотрена поддержка Классические приложения .net, .net core и Windows Универсальной приложений. В .NET core только перегрузка, принимающая имя пользователя доступен, как платформа .NET Core не может задавать имя пользователя для операционной системы.
  
Дополнительные сведения о согласие, см. в разделе [версии 2.0 разрешения и согласие](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>Использование

Обычно необходимо только один параметр (`scopes`). Тем не менее в зависимости от того, администратору Windows установил политики, это может быть возможно, что приложений на компьютере windows не может найти пользователя, вошедшего в систему. В этом случае следует использовать второй метод `.WithUsername()` и передайте имя вошедшего в систему пользователя в формате имени участника-пользователя - `joe@contoso.com`.

В следующем образце представлено наиболее текущего варианта, предоставив четкое описание типа исключения, которые можно получить и способов их устранения

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

Список возможных модификаторов для AcquireTokenByIntegratedWindowsAuthentication, см. в разделе [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>Имя пользователя и пароль

Вы также можете получить маркер, указав имя пользователя и пароль. Этот поток ограничено и не рекомендуется, но существуют по-прежнему использовать в случаях, где это необходимо.

### <a name="this-flow-isnt-recommended"></a>Этот поток не рекомендуется.

Этот поток является **не рекомендуется** так, как приложение, запрашивание у пользователя пароль не защищен. Дополнительные сведения об этой проблеме см. в разделе [в этой статье](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Основной поток для получения маркера без уведомления на компьютерах, присоединенных к домену Windows [встроенную проверку подлинности Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). В противном случае можно также использовать [потока кода устройства](https://aka.ms/msal-net-device-code-flow)

> [!NOTE] 
> Несмотря на то, что это полезно в некоторых случаях (сценарии DevOps), если вы хотите использовать имя пользователя и пароль в интерактивных сценариях, где ввести ваш onw пользовательского интерфейса, как следует по думать о том, как отойти от его. С помощью имени пользователя и пароля можно предоставляя вверх ряд действий:
>
> - основные концепции современного удостоверения: пароль получает fished, воспроизведения. Поскольку у нас есть эта концепция секрет общей папки, которые могут быть перехвачены.
> Это несовместимо с без пароля.
> - Пользователи, которым требуется проходить многофакторную Идентификацию не сможет войти (как никак не взаимодействует)
> - Пользователи не будут иметь возможность единого входа

### <a name="constraints"></a>Ограничения

Также действуют следующие ограничения:

- Потоку имя пользователя и пароль не совместим с условным доступом и многофакторной проверки подлинности: Как следствие Если приложение выполняется в клиенте Azure AD, где администратор клиента требует многофакторной проверки подлинности, нельзя использовать этот поток. Во многих организациях для этого.
- Он работает только для рабочих и учебных учетных записей (не MSA)
- Поток доступен на классические приложения .net и .net core, но не в UWP

### <a name="b2c-specifics"></a>Особенности B2C

[Дополнительные сведения об использовании ROPC с B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>Способы его использования?

`IPublicClientApplication`содержит метод `AcquireTokenByUsernamePassword`

В следующем образце представлено упрощенному случаю

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
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

В следующем образце представлено наиболее текущего варианта, предоставив четкое описание типа исключения, которые можно получить и способов их устранения

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
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

Дополнительные сведения о всех модификаторы, которые могут применяться к `AcquireTokenByUsernamePassword`, см. в разделе [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>Средство командной строки (без веб-браузер)

### <a name="device-code-flow-why-and-how"></a>Почему поток кода устройства? и как?

Если вы создаете средство командной строки (не обязательно, веб-элементы управления) и не могут или не хотите использовать предыдущий потоков, вам потребуется использовать `AcquireTokenWithDeviceCode`.

Интерактивная проверка подлинности с помощью Azure AD требуется браузер (Дополнительные сведения см. [использования веб-браузеров](https://aka.ms/msal-net-uses-web-browser)). Тем не менее для проверки подлинности пользователей на устройствах или операционных систем, которые не предоставляют веб-браузер, поток кода устройства позволяет пользователю использовать другое устройство (например другого компьютера или мобильного телефона) для входа интерактивном режиме. С помощью потока кода устройства, приложение получает маркеры через двухэтапный процесс, специально предназначенный для этих устройств/OS. Примерами таких приложений являются приложения для Интернета вещей или средства командной строки (CLI). Идея состоит в том:

1. Каждый раз, когда необходима проверка подлинности пользователя, приложение предоставляет код и предлагает пользователю, используйте другое устройство (например смартфонов с подключением к Интернету), чтобы перейти на URL-адрес (например, `https://microsoft.com/devicelogin`), где пользователю будет предложено ввести код. Что сделать, веб-страницу приводит пользователя посредством взаимодействия с обычной проверки подлинности, включая запросы согласия и многофакторной проверки подлинности, при необходимости.

2. После успешной проверки подлинности приложение командной строки получит необходимые маркеры по каналу обратной и будет использовать его для выполнения вызовов API web, которые необходимы.

### <a name="code"></a>Код

`IPublicClientApplication`содержит метод с именем `AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Этот метод принимает в качестве параметров:

- `scopes` Для запроса маркера доступа для
- Обратный вызов, который будет получать `DeviceCodeResult`

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

В следующем примере кода приведены наиболее текущего варианта, с помощью описания типа исключения, которые можно получить и их устранение.

```CSharp
static async Task<AuthenticationResult> GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication pca = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 AuthenticationResult result = null;
 var accounts = await app.GetAccountsAsync();

 // All AcquireToken* methods store the tokens in the cache, so check the cache first
 try
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
       .ExecuteAsync();
 }
 catch (MsalUiRequiredException ex)
 {
  // A MsalUiRequiredException happened on AcquireTokenSilent.
  // This indicates you need to call AcquireTokenInteractive to acquire a token
  System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
 }

 try
 {
  result = await app.AcquireTokenWithDeviceCode(scopes,
      deviceCodeCallback =>
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
  // If you use a CancellationToken, and call the Cancel() method on it, then this may be triggered
  // to indicate that the operation was cancelled.
  // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
  // for more detailed information on how C# supports cancellation in managed threads.
 }
 catch (MsalClientException ex)
 {
  // Verification code expired before contacting the server
  // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
  // call to `AcquireTokenWithDeviceCode` is not cancelled in between
 }
}
```

## <a name="file-based-token-cache"></a>Файл создан на основе кэша маркеров

В MSAL.NET по умолчанию предоставляется кэш маркеров в памяти.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>Сериализацию можно настраивать в приложениях для настольных систем Windows и web apps/веб-API

В случае с .NET Framework и .NET core Если этого не сделать, любые дополнительные, кэш маркеров в памяти продолжается в течение всего приложения. Чтобы понять, почему сериализации не предоставляется по умолчанию, помните, MSAL для .NET рабочего стола или основных приложений может быть консоли или приложения Windows (которые будет иметь доступ к файловой системе), **, но также** веб-приложения или веб-API. Эти веб-приложений и веб-API может использовать некоторые механизмы для конкретного кэша, как базы данных, распределенные кэши кэши redis и т. д. Чтобы это приложение постоянного кэша токенов в Классические приложения .NET или Core, необходимо настроить сериализацию.

Классы и интерфейсы, участвующих в кэш маркеров сериализации являются следующие типы:

- ``ITokenCache``, который определяет события для подписки на запросы сериализации кэш маркеров, а также методы для сериализации или десериализацию кэша в различных форматах (ADAL v3.0, MSAL 2.x и MSAL 3.x = ADAL версии 5.0)
- Обратный вызов ``TokenCacheCallback`` передается событиям, что позволяет обрабатывать операции сериализации. они будут называться с аргументами типа ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs`` предоставляет только ``ClientId`` приложения и ссылку на пользователя, для которого доступен маркер

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET создает кэши маркеров и предоставляет кэш `IToken` для работы методов `GetUserTokenCache` и `GetAppTokenCache` приложения. Вы не должны реализовать интерфейс. При реализации пользовательской сериализации для кэша маркеров в вашу зону ответственности входит следующее:
>
> - Реагировать на них `BeforeAccess` и `AfterAccess` «события» (или они *Async* какой). `BeforeAccess` Делегат отвечает для десериализации в кэш, тогда как `AfterAccess` один отвечает за сериализацию в кэш.
> - Часть этих событий сохраняют или загружают большие двоичные объекты, которые передаются через аргумент события в нужное хранилище.

Стратегии, другим в зависимости от того, если вы создаете кэш маркеров сериализация для общедоступного клиентского приложения (Desktop) или конфиденциального клиентского приложения (web приложение или веб-API, управляющую программу).

Так как версии 2.x MSAL можно несколькими способами, в зависимости от, если вы хотите сериализовать кэша только для формата MSAL.NET (кэш унифицированном формате, обычно с помощью MSAL, но и на всех платформах), или если также требуется для поддержки [устаревших](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) Сериализация кэш маркера ADAL версии 3.

Настройка сериализации кэша маркера для совместного использования состояния единого входа между ADAL.NET 3.x, ADAL.NET 5.x и MSAL.NET частично описан из следующего примера: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Простая сериализация кэша маркеров (только для MSAL)

Ниже приведен пример основной реализации пользовательской сериализации кэша маркеров для классических приложений. Здесь кэша маркера пользователя в файле в той же папке, что и приложение.

После построения приложения, включить сериализацию, вызвав ``TokenCacheHelper.EnableSerialization()`` передачи приложения `UserTokenCache`

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

Предварительный просмотр кэш маркеров качества продукта файловых сериализатор для общедоступных клиентских приложений (для классических приложений под управлением Windows, Mac и linux) доступен из [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) Библиотека с открытым исходным кодом. Это решение можно включить в приложение из следующего пакета NuGet: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Заявление об отказе. Библиотека Microsoft.Identity.Client.Extensions.Msal является расширением через MSAL.NET. Классы в этих библиотеках может сделать свой путь MSAL.NET в будущем, как есть или с критическими изменениями.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Сериализация двух кэш маркеров (кэша MSAL единой + ADAL версии 3)

Если вы хотите реализовать кэш маркеров сериализации с помощью единого кэшировать формат (общие для ADAL.NET 4.x и MSAL.NET 2.x и с другими MSALs той же версии или более ранней версии, на той же платформе), вы можете вдохновение в следующем примере кода :

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

Это время вспомогательный класс выглядит аналогично следующему коду:

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
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

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
> [Вызов веб-API из настольного приложения](scenario-desktop-call-api.md)
