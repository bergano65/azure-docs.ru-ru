---
title: Вызов приложений управляющей программы веб-API, (получение маркеров для приложения) - платформой Microsoft identity
description: Узнайте, как создать управляющую программу, вызовы веб-API-интерфейсы (приобретения токенов)
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
ms.openlocfilehash: aa4f5dc7a5aceaf81f71eacd36d131471a57e5c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075375"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Управляющая программа приложение, которое вызывает веб-интерфейсы API — получение маркера

После создания конфиденциального клиентского приложения, вы можете получить маркер для приложения, вызвав ``AcquireTokenForClient``, передача области действия и принудительно или не обновления токена.

## <a name="scopes-to-request"></a>Чтобы запросить области

Область запроса для поток учетных данных клиента совпадает с именем ресурса, за которым следует `/.default`. Эта запись сообщает Azure AD для использования **разрешения на уровне приложения** объявлять статически во время регистрации приложения. Кроме того, как показано ранее, эти API должны быть предоставлены разрешения администратором клиента

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

В MSAL. Python, файл конфигурации будет выглядеть как приведенный ниже фрагмент кода:

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

Область, используемую для учетных данных клиента всегда должно быть resourceId + "/ Параметры»

### <a name="case-of-v10-resources"></a>Случай v1.0 ресурсов

> [!IMPORTANT]
> Для MSAL (конечная точка версии 2.0), запрос маркера доступа для ресурса, принимая маркер доступа версии 1.0 Azure AD выполняет синтаксический анализ нужную аудиторию из запрошенной области все, что предшествует последний символ косой черты и использовать ее в качестве идентификатора ресурса.
> Таким образом Если, например Azure SQL ( **https://database.windows.net** ) ресурс ожидает аудитории заканчивается косой чертой (для Azure SQL: `https://database.windows.net/` ), вам потребуется запросить область `https://database.windows.net//.default` (Обратите внимание, двойная косая черта). См. также MSAL.NET проблема [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Опущен ресурса url завершающую косую черту, которое вызвало сбой проверки подлинности sql.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

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

#### <a name="application-token-cache"></a>Кэш маркеров приложения

В MSAL.NET `AcquireTokenForClient` использует **кэш маркеров приложения** (все остальные методы AcquireTokenXX использовать кэш токена пользователей) не вызывайте `AcquireTokenSilent` перед вызовом `AcquireTokenForClient` как `AcquireTokenSilent` использует **пользователя** кэш токена. `AcquireTokenForClient` проверяет **приложения** сам кэшировать маркер и обновляет его.

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

Если у вас нет библиотеки для выбранного языка, может потребоваться напрямую использовать протокол:

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

Дополнительные сведения см. в документации протокола: [Azure Active Directory версии 2.0 и клиента OAuth 2.0 поток учетных данных](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="did-you-use-the-resourcedefault-scope"></a>Вы использовали области действия ресурса/по умолчанию?

Если вы получаете сообщение об ошибке, информирующее, что вы использовали недопустимой области действия, вы, вероятно, использовали `resource/.default` области.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Возможно, следует предоставить согласие администратора? Он нужен приложений управляющей программы!

Если отобразится сообщение об ошибке при вызове API **недостаточно привилегий для выполнения операции**, администратору клиента нужно предоставить разрешения приложению. См. в шаге 6 регистра выше клиентское приложение.
Как правило, встречаются, и ошибка со следующим описанием ошибки, такие как:

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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Управляющая программа приложение — вызов веб-API](scenario-daemon-call-api.md)