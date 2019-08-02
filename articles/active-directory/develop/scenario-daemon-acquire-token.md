---
title: Приложение управляющей программы, вызывающее веб-API (получение маркеров для приложения) — платформа Microsoft Identity
description: Узнайте, как создать управляющее приложение, вызывающее веб-API (получение маркеров).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: 6a5f15aa5264c0abf87cb15f0468e8a3a924e0b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562355"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Управляющее приложение, вызывающее веб-API — получение маркера

После создания конфиденциального клиентского приложения можно получить маркер для приложения путем вызова ``AcquireTokenForClient``, передачи области и принудительного или необновления маркера.

## <a name="scopes-to-request"></a>Области для запроса

Область запроса учетных данных клиента — это имя ресурса, за которым следует `/.default`. Эта нотация указывает, что Azure AD будет использовать **разрешения уровня приложения** , объявленные статически во время регистрации приложения. Кроме того, как было показано ранее, эти разрешения API должны быть предоставлены администратором клиента.

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

В MSAL. Python файл конфигурации будет выглядеть, как в следующем фрагменте кода:

```Python
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "secret": "This is a sample only. You better NOT persist your password."
    "scope": ["https://graph.microsoft.com/.default"]
}
```

### <a name="java"></a>Java

```Java
public final static String KEYVAULT_DEFAULT_SCOPE = "https://vault.azure.net/.default";
```

### <a name="all"></a>Все

Область, используемая для учетных данных клиента, всегда должна быть resourceId + "/.дефаулт"

### <a name="case-of-azure-ad-v10-resources"></a>Ресурсы Azure AD (v 1.0)

> [!IMPORTANT]
> Для MSAL (конечная точка платформы Microsoft Identity), запрашивающей маркер доступа для ресурса, принимающего маркер доступа версии 1.0, Azure AD анализирует нужную аудиторию из запрошенной области, принимая все до последней косой черты и используя ее в качестве идентификатора ресурса.
> Таким образом, если, как и **https://database.windows.net** в Azure SQL (), ресурс ожидает, что аудитория заканчивается косой чертой `https://database.windows.net/` (для Azure SQL:), необходимо запросить область `https://database.windows.net//.default` (Обратите внимание на двойную косую черту). См. также раздел MSAL.NET Issue [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Конечная косая черта ресурса не указана, что привело к сбою проверки подлинности SQL.

## <a name="acquiretokenforclient-api"></a>API Аккуиретокенфорклиент

### <a name="net"></a>.NET

```CSharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

#### <a name="application-token-cache"></a>Кэш маркеров приложений

В MSAL.NET использует `AcquireTokenForClient` **кэш маркеров приложений** (все остальные методы аккуиретокенкскс, использующие кэш пользовательских маркеров `AcquireTokenSilent` ), `AcquireTokenForClient` не вызывают метод, так как `AcquireTokenSilent` использует кэш **пользовательских** маркеров. `AcquireTokenForClient`проверяет сам кэш маркера **приложения** и обновляет его.

### <a name="python"></a>Python

```Python
# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

### <a name="java"></a>Java

```Java
ClientCredentialParameters parameters = ClientCredentialParameters
        .builder(Collections.singleton(KEYVAULT_DEFAULT_SCOPE))
        .build();

CompletableFuture<AuthenticationResult> future = cca.acquireToken(parameters);

// You can complete the future in many different ways. Here we use .get() for simplicity
AuthenticationResult result = future.get();
```

### <a name="protocol"></a>Протокол

Если вы еще не создали библиотеку для выбранного языка, вы можете использовать протокол напрямую:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Первый вариант. Запрос маркера доступа с помощью общего секрета

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Второй вариант. Запрос маркера доступа с помощью сертификата

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

### <a name="learn-more-about-the-protocol"></a>Дополнительные сведения о протоколе

Дополнительные сведения см. в документации по протоколу: [Платформа удостоверений Майкрософт и поток учетных данных клиента OAuth 2,0](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="did-you-use-the-resourcedefault-scope"></a>Вы использовали область ресурсов/. по умолчанию?

Если появляется сообщение об ошибке, сообщающее, что вы использовали недопустимую область, то, вероятно `resource/.default` , вы не использовали эту область.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Вы забыли предоставить согласие администратора? Требуются приложения управляющей программы!

Если при вызове API **недостаточно прав для выполнения операции**возникает ошибка, администратор клиента должен предоставить приложению разрешения. См. шаг 6 в разделе Регистрация клиентского приложения выше.
Как правило, отображается сообщение об ошибке, похожее на следующее описание ошибки:

```JSon
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

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Управляющая программа — вызов веб-API](scenario-daemon-call-api.md)