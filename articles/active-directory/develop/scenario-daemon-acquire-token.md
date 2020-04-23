---
title: Приобрести токены для вызова веб-API (приложение daemon) - платформа идентификации Microsoft Azure
description: Узнайте, как создать приложение daemon, которое вызывает веб-AIS (приобретение токенов)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d755573b53eb63d85165fb73fe4b97298dbeff09
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868997"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Приложение Daemon, которое вызывает веб-AIS - приобрести токен

После создания конфиденциального клиентского приложения вы можете приобрести токен `AcquireTokenForClient`для приложения, позвонив, пройдя область и дополнительно заставив обновить токен.

## <a name="scopes-to-request"></a>Области запроса

Область запроса потока учетных данных клиента — `/.default`это имя ресурса, за которым следует. Эта нотация говорит Active Directory Azure (Azure AD) использовать *разрешения уровня приложения,* объявленные статически при регистрации приложения. Кроме того, эти разрешения API должны быть предоставлены администратором-арендатором.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

В MSAL Python файл конфигурации выглядит как фрагмент кода:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="azure-ad-v10-resources"></a>Ресурсы Azure AD (v1.0)

Область, используемая для учетных данных клиента, всегда должна быть идентификатором `/.default`ресурсов, за которым следует.

> [!IMPORTANT]
> Когда MSAL запрашивает токен доступа для ресурса, который принимает токен доступа версии 1.0, Azure AD разбирает нужную аудиторию из запрашиваемых областей, забрав все до последней слэши и используя ее в качестве идентификатора ресурса.
> Так что, если, как Azure S'L базы данных **(https:\// database.windows.net**), ресурс ожидает аудитории, которая заканчивается с чертой (для базы данных Azure S'L, `https://database.windows.net/`), вам нужно будет запросить область `https://database.windows.net//.default`. (Обратите внимание на двойную слэш.) Смотрите также MSAL.NET вопрос [#747: Ресурс URL в задней черта опущена, что вызвало квл-хэт сбоя](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

Чтобы приобрести токен для приложения, `AcquireTokenForClient` вы будете использовать или его эквивалент, в зависимости от платформы.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

# <a name="java"></a>[Java](#tab/java)

Этот код извлекается из [образцов разработчиков MSAL Java.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

---

### <a name="protocol"></a>Протокол

Если у вас еще нет библиотеки для выбранного языка, вы можете использовать протокол напрямую:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>Первый случай: Доступ к запросу маркера с помощью общего секрета

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Второй случай: Доступ к запросу токенов с помощью сертификата

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

Для получения дополнительной информации ознакомьтесь с документацией по протоколу: [платформа идентификации Майкрософт и поток учетных данных клиентов OAuth 2.0.](v2-oauth2-client-creds-grant-flow.md)

## <a name="application-token-cache"></a>Кэш токенов приложений

В MSAL.NET `AcquireTokenForClient` использует кэш маркеров приложения. (Все остальные методы AcquireToken*XX* используют кэш маркеров пользователя.) Не звоните `AcquireTokenSilent` перед `AcquireTokenForClient`вызовом, потому что `AcquireTokenSilent` использует кэш *маркера пользователя.* `AcquireTokenForClient`проверяет сам кэш *маркера приложения* и обновляет его.

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="did-you-use-the-resourcedefault-scope"></a>Вы использовали область ресурса/.по умолчанию?

Если вы получили сообщение об ошибке, в котором говорилось, что `resource/.default` вы использовали недействительную область, вы, вероятно, не использовали область действия.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Вы забыли предоставить согласие на админ? Приложения Daemon нуждаются в этом!

Если при **Insufficient privileges to complete the operation** вызове API у администратора администратора-клиента требуется предоставить разрешения приложению. Смотрите шаг 6 Регистрация клиентского приложения выше.
Обычно вы увидите ошибку, которая выглядит как эта ошибка:

```json
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Приложение Daemon - вызов веб-API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Приложение Daemon - вызов веб-API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Приложение Daemon - вызов веб-API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
