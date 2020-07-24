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
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: eff5f68569d1878e1b802f2db4151d246bcc07c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026430"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Веб-API, вызывающий веб-API: конфигурация кода

После регистрации веб-API можно настроить код для приложения.

Код, используемый для настройки веб-API таким образом, чтобы он вызывал нисходящие веб-API на основе кода, используемого для защиты веб-API. Дополнительные сведения см. в разделе [защищенный веб-API: Конфигурация приложения](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>Секреты клиента или сертификаты клиента

Учитывая, что веб-API теперь вызывает нисходящий веб-API, необходимо предоставить секрет клиента или сертификат клиента в *appsettings.js* в файле.

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
 }
}
```

Microsoft. Identity. Web предоставляет несколько способов описания сертификатов как по конфигурации, так и по коду. Дополнительные сведения см. в разделе [Microsoft. Identity. Web wiki — использование сертификатов](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) на GitHub.

## <a name="startupcs"></a>Startup.cs

Используя Microsoft. Identity. Web, если вы хотите, чтобы веб-API вызывал нисходящие веб-API, добавьте `.AddMicrosoftWebApiCallsWebApi()` строку после `.AddMicrosoftWebApiAuthentication(Configuration)` , а затем выберите реализацию кэша маркеров, например `.AddInMemoryTokenCaches()` в *Startup.CS*:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  ...
  public void ConfigureServices(IServiceCollection services)
  {
   // ...
   services.AddMicrosoftWebApiAuthentication(Configuration)
           .AddMicrosoftWebApiCallsWebApi()
           .AddInMemoryTokenCaches();
  // ...
  }
  // ...
}
```

Как и в случае с веб-приложениями, можно выбрать различные реализации кэша маркеров. Дополнительные сведения см. в [статье сериализация кэша маркеров](https://aka.ms/ms-id-web/token-cache-serialization) на сайте GitHub с веб-сайта Microsoft Identity.

Если вы уверены, что веб-API потребует определенных областей, при необходимости можно передать их в качестве аргументов в `AddMicrosoftWebApiCallsWebApi` .

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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Веб-API, вызывающий веб-API: получение маркера для приложения](scenario-web-api-call-api-acquire-token.md)
