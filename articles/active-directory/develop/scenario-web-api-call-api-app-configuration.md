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
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 88f0891e9bd67da094240b059750226402da0244
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396237"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Веб-API, вызывающий веб-API: конфигурация кода

После регистрации веб-API можно настроить код для приложения.

Код, используемый для настройки веб-API таким образом, чтобы он вызывал нисходящие веб-API на основе кода, используемого для защиты веб-API. Дополнительные сведения см. в разделе [защищенный веб-API: Конфигурация приложения](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Microsoft. Identity. Web

Корпорация Майкрософт рекомендует использовать пакет NuGet [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) при разработке ASP.NET Core ЗАЩИЩЕНного API, вызывающего нисходящие веб-API. См [. раздел защищенный веб-API: конфигурация кода | Microsoft. Identity. Web](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) для быстрого представления этой библиотеки в контексте веб-API.

## <a name="client-secrets-or-client-certificates"></a>Секреты клиента или сертификаты клиента

Учитывая, что веб-API теперь вызывает нисходящий веб-API, необходимо предоставить секрет клиента или сертификат клиента в *appsettings.js* в файле. Можно также добавить раздел, который указывает:

- URL-адрес подчиненного веб-API
- Области, необходимые для вызова API

В следующем примере `GraphBeta` эти параметры задаются в разделе.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

Вместо секрета клиента можно предоставить сертификат клиента. В следующем фрагменте кода показано использование сертификата, хранящегося в Azure Key Vault.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

Microsoft. Identity. Web предоставляет несколько способов описания сертификатов как по конфигурации, так и по коду. Дополнительные сведения см. в разделе [Microsoft. Identity. Web wiki — использование сертификатов](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) на GitHub.

## <a name="startupcs"></a>Startup.cs

Веб-API должен получить маркер для подчиненного API. Его можно указать, добавив `.EnableTokenAcquisitionToCallDownstreamApi()` строку после `.AddMicrosoftIdentityWebApi(Configuration)` . Эта строка предоставляет `ITokenAcquisition` службу, которую можно использовать в действиях контроллера или страниц. Однако, как вы увидите в следующих двух маркированных точках, вы можете сделать еще более простым. Также необходимо выбрать реализацию кэша маркеров, например `.AddInMemoryTokenCaches()` в *Startup.CS*:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Если вы не хотите самостоятельно получать маркер, *Microsoft. Identity. Web* предоставляет два механизма вызова подчиненного веб-API из другого API. Выбор варианта зависит от того, требуется ли вызывать Microsoft Graph или другой API.

### <a name="option-1-call-microsoft-graph"></a>Вариант 1. вызов Microsoft Graph

Если вы хотите вызвать Microsoft Graph, Microsoft. Identity. Web позволяет напрямую использовать `GraphServiceClient` (предоставляется Microsoft Graph SDK) в действиях API. Чтобы предоставить Microsoft Graph:

1. Добавьте в проект пакет NuGet [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) .
1. Добавьте `.AddMicrosoftGraph()` после `.EnableTokenAcquisitionToCallDownstreamApi()` в файл *Startup.CS* . `.AddMicrosoftGraph()` имеет несколько переопределений. При использовании переопределения, которое принимает раздел конфигурации в качестве параметра, код становится следующим:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Вариант 2. вызов подчиненного веб-интерфейса API, отличного от Microsoft Graph

Для вызова подчиненного API, отличного от Microsoft Graph, *Microsoft. Identity. Web* предоставляет `.AddDownstreamWebApi()` , который запрашивает маркеры и вызывает нисходящий веб-API.

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Как и в случае с веб-приложениями, можно выбрать различные реализации кэша маркеров. Дополнительные сведения см. в [статье сериализация кэша веб-маркеров Microsoft Identity](https://aka.ms/ms-id-web/token-cache-serialization) на сайте GitHub.

На следующем рисунке показаны различные возможности *Microsoft. Identity. Web* и их влияние на файл *Startup.CS* :

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.png" alt-text="При создании веб-API можно выбрать вызов нисходящего API и реализаций кэша маркеров.":::

> [!NOTE]
> Чтобы полностью понять эти примеры кода, ознакомьтесь с [основами ASP.NET Core](/aspnet/core/fundamentals), в частности со сведениями о [параметрах](/aspnet/core/fundamentals/configuration/options) и [внедрении зависимостей](/aspnet/core/fundamentals/dependency-injection).

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

Дополнительные сведения о протоколе OBO см. [в статье о платформе Microsoft Identity и OAuth 2,0 от имени потока](./v2-oauth2-on-behalf-of-flow.md).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Веб-API, вызывающий веб-API: получение маркера для приложения](scenario-web-api-call-api-acquire-token.md)
