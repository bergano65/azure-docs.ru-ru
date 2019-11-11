---
title: Защищенный веб-API — конфигурация кода приложения
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: eb562caf2dfc83841762748f37d1a7ee325de10b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73882625"
---
# <a name="protected-web-api-code-configuration"></a>Защищенный веб-API: конфигурация кода

Чтобы настроить код для защищенного веб-API, необходимо понять, что определяет интерфейсы API как защищенные, как настроить токен носителя и как проверить маркер.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>Что определяет API-интерфейсы ASP.NET/ASP.NET Core как защищенные?

Как и веб-приложения, веб-API ASP.NET/ASP.NET Core являются защищенными, так как их действия контроллера начинаются с атрибута `[Authorize]`. Поэтому действия контроллера можно вызывать только в том случае, если API вызывается с удостоверением, которому разрешено.

Учитывайте следующие вопросы.

- Как веб-API знает удостоверение приложения, которое его вызывает? (Веб-API может вызывать только приложение.)
- Если приложение называется веб-API от имени пользователя, удостоверение пользователя?

## <a name="bearer-token"></a>Маркер носителя

Сведения об удостоверении приложения и о пользователе (если веб-приложение не принимает вызовы между службами из управляющего приложения) хранятся в токене носителя, который задается в заголовке при вызове приложения.

Ниже приведен пример C# кода, в котором показан клиент, вызывающий API после получения маркера с помощью библиотеки проверки подлинности Майкрософт для .net (MSAL.NET):

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
> Маркер носителя был запрошен клиентским приложением в конечной точке платформы идентификации Майкрософт *для веб-API*. Веб-API — это единственное приложение, которое должно проверять маркер и просматривать содержащиеся в нем утверждения. Клиентские приложения никогда не должны пытаться проверить утверждения в токенах. (В будущем веб-API может потребовать, чтобы маркер был зашифрован. Это требование помешает доступу к клиентским приложениям, которые могут просматривать маркеры доступа.)

## <a name="jwtbearer-configuration"></a>Конфигурация JwtBearer

В этом разделе описывается настройка токена носителя.

### <a name="config-file"></a>Файл конфигурации

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
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

### <a name="code-initialization"></a>Инициализация кода

При вызове приложения в действии контроллера, которое содержит атрибут `[Authorize]`, ASP.NET/ASP.NET Core просматривает маркер носителя в заголовке авторизации вызывающего запроса и извлекает маркер доступа. Затем маркер пересылается по промежуточного слоя JwtBearer, который вызывает расширения Microsoft IdentityModel для .NET.

В ASP.NET Core это по промежуточного слоя инициализируется в файле Startup.cs:

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

По промежуточного слоя добавляется в веб-API с помощью следующей инструкции:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 В настоящее время шаблоны ASP.NET Core создают веб-API Azure Active Directory (Azure AD), которые входят в вашу организацию или в любую организацию, а не в личные учетные записи. Но вы можете легко изменить их, чтобы использовать конечную точку платформы идентификации Майкрософт, добавив следующий код в файл Startup.cs:

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

Этот фрагмент кода извлекается из пошагового руководства по ASP.NET Core веб-API в [Microsoft. Identity. Web/вебаписервицеколлектионекстенсионс. CS # L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). Метод `AddProtectedWebApi`, который выполняет гораздо больше, вызывается из Startup.cs

## <a name="token-validation"></a>Проверка маркеров

По промежуточного слоя JwtBearer (например, по промежуточного слоя OpenID Connect Connect в веб-приложениях) направляется `TokenValidationParameters` для проверки маркера. Маркер расшифровывается (при необходимости), извлекаются утверждения и проверяется подпись. По промежуточного слоя затем проверяет маркер, проверяя эти данные:

- Он предназначен для веб-API (аудитории).
- Она была выдана для приложения, которому разрешено вызывать веб-API (подпрограмму).
- Она была выдана доверенной службой маркеров безопасности (STS) (Issuer).
- Его время существования находится в диапазоне (срок действия).
- Он не был изменен (сигнатура).

Также могут быть специальные проверки. Например, можно проверить, что ключи подписывания (внедренные в маркере) являются доверенными и что этот маркер не воспроизводится. Наконец, для некоторых протоколов требуются определенные проверки.

### <a name="validators"></a>Проверяющие элементы управления

Шаги проверки фиксируются в средствах проверки, которые находятся в библиотеке [Microsoft IdentityModel Extensions для .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) с открытым исходным кодом, в одном исходном файле: [Microsoft. IdentityModel. Tokens/проверяющие. CS](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

В этой таблице описаны проверяющие элементы управления.

| Управления | ОПИСАНИЕ |
|---------|---------|
| `ValidateAudience` | Гарантирует, что маркер предназначен для приложения, которое проверяет маркер (для меня). |
| `ValidateIssuer` | Гарантирует, что маркер был выдан доверенной службой STS (от доверенного лица). |
| `ValidateIssuerSigningKey` | Гарантирует, что приложение, проверяющее маркер, доверяет ключу, который использовался для подписания маркера. (Особый случай, когда ключ внедряется в маркер. Обычно не требуется.) |
| `ValidateLifetime` | Гарантирует, что маркер все еще (или уже) является допустимым. Проверяющий элемент управления проверяет, находится ли время существования маркера (`notbefore` и `expires` утверждений) в диапазоне. |
| `ValidateSignature` | Гарантирует, что маркер не был изменен. |
| `ValidateTokenReplay` | Гарантирует, что маркер не воспроизводится. (Особый случай для некоторых OneTime использования протоколов.) |

Все проверяющие элементы управления связаны со свойствами класса `TokenValidationParameters`, которые самостоятельно инициализируются из конфигурации ядра ASP.NET/ASP.NET. В большинстве случаев изменять параметры не требуется. Существует одно исключение для приложений, которые не являются отдельными клиентами. (То есть веб-приложения, которые принимают пользователей из любой организации или из личных учетных записей Майкрософт.) В этом случае издатель должен быть проверен.

## <a name="token-validation-in-azure-functions"></a>Проверка токенов в функциях Azure

Также можно проверить входящие маркеры доступа в функциях Azure. Примеры проверки маркеров в функциях Azure можно найти в [DotNet](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)и [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Проверка областей и ролей приложений в коде](scenario-protected-web-api-verification-scope-app-roles.md)
