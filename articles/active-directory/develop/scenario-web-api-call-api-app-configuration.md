---
title: Настройка веб-API, вызывающего веб-API | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как создать веб-API, который вызывает веб-API (конфигурация кода приложения).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 38e319efb100d326d55f6f821e7c903306a7c7d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80991013"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Веб-API, вызывающий веб-API: конфигурация кода

После регистрации веб-API можно настроить код для приложения.

Код, используемый для настройки веб-API таким образом, чтобы он вызывал нисходящие веб-API на основе кода, используемого для защиты веб-API. Дополнительные сведения см. в разделе [защищенный веб-API: Конфигурация приложения](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>Код подписан на Онтокенвалидатед

На основе конфигурации кода для любых защищенных веб-API необходимо подписываться на проверку токена носителя, полученного при вызове API:

```csharp
/// <summary>
/// Protects the web API with the Microsoft identity platform, or Azure Active Directory (Azure AD) developer platform
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">The service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it
        // to the MSAL.NET cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache and handles the incremental consent
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Поток On-Behalf-Of

Метод Аддаккаунттокачефромжвт () должен:

- Создание экземпляра конфиденциального клиентского приложения библиотеки проверки подлинности Microsoft (MSAL).
- Вызовите метод `AcquireTokenOnBehalf`. Этот вызов обменивается токеном носителя, который был получен клиентом для веб-API, по токену носителя для того же пользователя, но у него есть API, который вызывает нисходящий API.

### <a name="instantiate-a-confidential-client-application"></a>Создание экземпляра конфиденциального клиентского приложения

Этот поток доступен только в конфиденциальном потоке клиента, поэтому защищенный веб-API предоставляет учетные данные клиента (секрет клиента или сертификата) [классу конфидентиалклиентаппликатионбуилдер](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) с помощью `WithClientSecret` `WithCertificate` метода или.

![Список методов Иконфидентиалклиентаппликатион](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```csharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

Наконец, вместо подтверждения своей личности с помощью секрета клиента или сертификата конфиденциальные клиентские приложения могут доказать свою личность с помощью утверждений клиента.
Дополнительные сведения об этом расширенном сценарии см. в разделе [конфиденциальные утверждения клиента](msal-net-client-assertions.md).

### <a name="how-to-call-on-behalf-of"></a>Как позвонить от имени

Чтобы выполнить вызов on-of (OBO), вызовите [метод аккуиретокенонбехалф](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) в `IConfidentialClientApplication` интерфейсе.

`UserAssertion`Класс строится на основе токена носителя, полученного веб-API от собственных клиентов. Существует [два конструктора](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet):
* Один, принимающий токен носителя JSON Web Token (JWT)
* Один, принимающий любое утверждение пользователя, другой вид маркера безопасности, тип которого затем указывается в дополнительном параметре с именем`assertionType`

![Свойства и методы Усерассертион](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

На практике поток OBO часто используется для получения маркера для подчиненного API и сохранения его в кэше пользовательских маркеров MSAL.NET. Это делается для того, чтобы другие части веб-API могли впоследствии вызывать [переопределения](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) ``AcquireTokenOnSilent`` для вызова интерфейсов API нисходящей связи. Этот вызов оказывает воздействие обновления токенов при необходимости.

```csharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```
# <a name="java"></a>[Java](#tab/java)

Поток "от имени" (OBO) используется для получения маркера для вызова подчиненного веб-API. В этом потоке веб-API получает маркер носителя с делегированными разрешениями из клиентского приложения, а затем обменивается этим маркером с другим маркером доступа для вызова подчиненного веб-API.

Приведенный ниже код использует `SecurityContextHolder` в веб-API каркасную платформу для обеспечения безопасности, чтобы получить проверенный токен носителя. Затем она использует библиотеку Java MSAL для получения маркера для подчиненного API с помощью `acquireToken` вызова с `OnBehalfOfParameters` . MSAL кэширует токен таким образом, чтобы последующие вызовы API могли использовать `acquireTokenSilently` для получения кэшированного маркера.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Поток "от имени" (OBO) используется для получения маркера для вызова подчиненного веб-API. В этом потоке веб-API получает маркер носителя с делегированными разрешениями из клиентского приложения, а затем обменивается этим маркером с другим маркером доступа для вызова подчиненного веб-API.

Веб-API Python должен использовать некоторое по промежуточного слоя для проверки токена носителя, полученного от клиента. Затем веб-API может получить маркер доступа для подчиненного API с помощью библиотеки MSAL Python, вызвав [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) метод. Пример использования этого API см. в [коде теста для Microsoft-Authentication-Library-для-Python на сайте GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). См. также описание [проблемы 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) в том же репозитории для подхода, который обходит потребность в приложении среднего уровня.

---

Вы также можете увидеть пример реализации OBO Flow в [Node.js и функциях Azure](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Протокол

Дополнительные сведения о протоколе OBO см. [в статье о платформе Microsoft Identity и OAuth 2,0 от имени потока](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Веб-API, вызывающий веб-API: получение маркера для приложения](scenario-web-api-call-api-acquire-token.md)
