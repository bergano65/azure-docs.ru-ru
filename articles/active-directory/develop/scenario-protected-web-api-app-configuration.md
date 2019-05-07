---
title: Защищенный веб-API - код конфигурации приложений | Azure
description: Узнайте, как создать защищенный веб-API и настроить код приложения.
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
ms.openlocfilehash: e206cb29338445e30a7462bcbaf0079236e75510
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074970"
---
# <a name="protected-web-api---code-configuration"></a>Защищенный веб-API - код конфигурации

Чтобы успешно настроить код для защищенных веб-API, необходимо понимать, что делает API, защищенному, вам нужно настроить токен носителя и способы проверки маркера.

## <a name="what-makes-aspnetaspnet-core-apis-protected"></a>Что делает ASP.NET/ASP.NET Core API, защищенному?

Как и в веб-приложения, ASP.NET/ASP.NET core web API-интерфейсы «защищены», так как их действий контроллера имеют префикс `[Authorize]` атрибута. Таким образом действия контроллера может вызываться только при вызове API с удостоверением, которая имеет права.

Ответьте на следующие вопросы:

- Как веб-API, что удостоверение приложения, которая вызывает его (только приложение может вызвать веб-API)?
- Если приложение вызвало веб-API от имени пользователя, что такое удостоверение пользователя?

## <a name="bearer-token"></a>Маркер носителя

Сведения об удостоверении приложения и пользователь (если веб-приложение принимает вызовы service to service из приложения управляющей программы), хранящиеся в токен носителя, который задается в заголовке, при вызове приложения.

Вот C# пример кода, показывающий вызова API после получения маркера с помощью MSAL.NET клиента.

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Токен носителя была запрошена клиентским приложением к конечной точке платформы удостоверений Microsoft **для веб-API**. Веб-API — это единственное приложение, которое следует проверить токен и посмотрите на утверждения, содержащиеся в ней. Клиентские приложения никогда не следует обратить внимание на утверждениях в маркерах, (веб-API может решить, в будущем, он требует, чтобы маркер были зашифрованы, и это приведет к разрыву клиентском приложении, которое можно взломать откройте маркеры доступа).

## <a name="jwtbearer-configuration"></a>JwtBearer конфигурации

В этом разделе приводятся необходимые для настройки токена носителя.

### <a name="config-file"></a>Файл конфигурации

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line of business app)
      Otherwise you can leave them set to common
      This can be:
      - A guid (Tenant ID = Directory ID)
      - 'common' (Any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="code-initialization"></a>Код инициализации

При вызове приложение в ожидании действия контроллера `[Authorize]` атрибут, ASP.NET/ASP.NET core проверяет токен носителя в заголовке авторизации запроса, вызывающего и извлекает маркер доступа, который затем передается JwtBearer по промежуточного слоя, который вызывает расширения модели удостоверений Майкрософт для .NET.

В ASP.NET Core, это по промежуточного слоя инициализируется в `Startup.cs` файл.

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

По промежуточного слоя добавляется веб-API, следующая инструкция:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 В настоящее время шаблоны ASP.NET Core создать Azure AD версии 1.0 в веб-API. Тем не менее, их использование конечной точки платформы Microsoft identity, добавив следующий код в можно легко изменить `Startup.cs` файл.

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API
    options.Authority += "/v2.0";

    // The web API accepts as audiences are both the Client ID (options.Audience) and api://{ClientID}
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line of business apps),
    // we inject our own multi-tenant validation logic (which even accepts both V1 and V2 tokens)
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Проверка маркеров

По промежуточного слоя JwtBearer, такие как по промежуточного слоя OpenID Connect в веб-приложениях, направляется по `TokenValidationParameters` для проверки токена. Расшифровки маркера (при необходимости), извлекаются утверждения и выполняется проверка сигнатуры. Затем токен проверяется, проверив наличие следующих данных:

- Она предназначена для веб-API (аудитории)
- Он выдан для приложения, которое может вызвать веб-API (sub)
- Он выдан доверять безопасности маркера сервера (STS) (издатель)
- Время существования маркера находится в диапазоне (срок действия)
- Он не был подделан (подпись)

Также может быть необязательно. Например это можно проверить, что ключи подписывания (при его внедрении в токене) являются доверенными и не воспроизводится маркер. Наконец некоторые протоколы требуют определенных проверок.

### <a name="validators"></a>Проверяющие элементы управления

Шаги проверки регистрируются в проверяющих элементов управления, которые входят в [расширения модели удостоверений Майкрософт для .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) библиотеки с открытым исходным кодом, в одном исходном файле: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)

Проверяющие элементы управления описаны в следующей таблице:

| Проверяющий элемент управления | ОПИСАНИЕ |
|---------|---------|
| `ValidateAudience` | Гарантирует, что маркер является для приложения, которое проверяет токен (для me). |
| `ValidateIssuer` | Гарантирует, что маркер был выдан доверенные STS (от кого-то, которое я доверяю). |
| `ValidateIssuerSigningKey` | Гарантирует приложения, проверка маркера доверие ключ, который использовался для подписи маркера (особый случай, где ключ внедряется в маркере, это обычно не требуется). |
| `ValidateLifetime` | Гарантирует, что маркер является допустимым по-прежнему (или ранее). Сделать, установив флажок, время жизни токена (`notbefore`, `expires` утверждений) находится в диапазоне. |
| `ValidateSignature` | Гарантирует, что токен еще не были изменены. |
| `ValidateTokenReplay` | Гарантирует маркер не воспроизводятся (особый случай для некоторых протоколов onetime использования). |

Проверяющие элементы управления все связанные со свойствами объекта `TokenValidationParameters` класса, сами инициализирован из конфигурации ASP.NET/ASP.NET Core. В большинстве случаев не придется изменять параметры. Есть одно исключение для приложений, которые не являются одним клиентом (то есть веб-приложений, которые принимают пользователей из любой организации или личных учетных записей Майкрософт) — в этом случае необходимо проверить издателя.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Переместить в рабочую среду](scenario-protected-web-api-production.md)
