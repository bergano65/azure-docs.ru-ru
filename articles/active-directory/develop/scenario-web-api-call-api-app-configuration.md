---
title: Налаживать веб-aPI, который вызывает веб-API(ru) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как создать веб-aPI, который вызывает web API (конфигурация кода приложения)
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
ms.openlocfilehash: 82b5e1d9753fbb65fd81f24b06016d302457144e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834099"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Веб-aPI, который вызывает веб-API: конфигурация кода

После регистрации веб-API можно настроить код приложения.

Код, используемый для настройки веб-API таким образом, чтобы он вызывает аГИТ-сосуды ниже по течению, строится поверх кода, используемого для защиты веб-API. Для получения дополнительной информации [см.](scenario-protected-web-api-app-configuration.md)

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>Код, подписанный на OnTokenValidated

Помимо конфигурации кода для любых защищенных web-aPI, необходимо подписаться на проверку маркера носителя, который вы получаете при вызове API:

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

## <a name="on-behalf-of-flow"></a>Поток на behalf-Of

Метод AddAccountToCacheFromJwt() должен:

- Мгновенное приложение Microsoft Authentication Library (MSAL) конфиденциального клиента.
- Вызовите метод `AcquireTokenOnBehalf` . Этот вызов обменивает токен носителя, который был приобретен клиентом для веб-API, на токен носителя для того же пользователя, но у него есть API вызова ниже по течению API.

### <a name="instantiate-a-confidential-client-application"></a>Мгновенное конфиденциальное приложение клиента

Этот поток доступен только в конфиденциальном клиентском потоке, так что защищенный веб-aPI предоставляет учетным `WithClientSecret` данным `WithCertificate` клиента (секрет клиента или сертификат) [классу ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) либо с помощью метода.

![Список методов IConfidentialClientApplication](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

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

Наконец, вместо того, чтобы доказать свою личность с помощью секрета клиента или сертификата, конфиденциальные клиентские приложения могут доказать свою личность с помощью утверждений клиентов.
Для получения дополнительной информации об этом расширенном сценарии [см.](msal-net-client-assertions.md)

### <a name="how-to-call-on-behalf-of"></a>Как позвонить в On-Behalf-Of

Вы делаете вызов On-Behalf-Of (OBO), позвонив в `IConfidentialClientApplication` метод [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) на интерфейсе.

Класс `UserAssertion` построен из токена предъявителя, полученного web API от собственных клиентов. Есть [два конструктора:](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)
* Тот, который принимает маркер jSON Web (JWT) маркер предъявителя
* Тот, который принимает любой вид утверждения пользователя, другой вид маркера безопасности, тип которого затем указан в дополнительном параметре, названном`assertionType`

![Свойства и методы UserAssertion](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

На практике поток OBO часто используется для приобретения токена для aPI ниже по течению и его хранения в MSAL.NET кэша маркеров пользователя. Это делается для того, ``AcquireTokenOnSilent`` чтобы другие части Web API позже могли вызвать [переопределения](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) для вызова апогеях ниже по течению. Этот вызов имеет эффект обновления токенов, если это необходимо.

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

Поток On-behalf-of (OBO) используется для получения токена для вызова нижепого веб-API. В этом потоке ваш веб-API получает токен носителя с делегированными пользователями разрешениями от клиентского приложения, а затем обменивает этот маркер на другой токен доступа, чтобы вызвать нижепом веб-API.

Приведенный ниже код использует `SecurityContextHolder` платформу Spring Security в веб-API для получения токена подтвержденного носителя. Затем он использует библиотеку MSAL Java для получения маркера `acquireToken` для `OnBehalfOfParameters`нижепого API с помощью вызова с помощью. MSAL кэширует токен, чтобы последующие вызовы в API могли использовать `acquireTokenSilently` для получения кэшированного токена.

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

Поток On-behalf-of (OBO) используется для получения токена для вызова нижепого веб-API. В этом потоке ваш веб-API получает токен носителя с делегированными пользователями разрешениями от клиентского приложения, а затем обменивает этот маркер на другой токен доступа, чтобы вызвать нижепом веб-API.

Веб-API Python должен будет использовать некоторые промежуточное программное обеспечение для проверки маркера предъявителя, полученного от клиента. Веб-API может получить токен доступа для ниже по течению API с помощью библиотеки MSAL Python, позвонив в [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) метод. Образец, демонстрирующий этот поток с помощью MSAL Python, пока недоступен.

---

Вы также можете увидеть пример реализации потока OBO в [функциях Node.js и Azure.](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61)

## <a name="protocol"></a>Протокол

Для получения дополнительной информации о протоколе OBO, см [Microsoft идентификационную платформу и OAuth 2.0 On-Behalf-Of потока](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Веб-aPI, который вызывает веб-API: Приобрести маркер для приложения](scenario-web-api-call-api-acquire-token.md)
