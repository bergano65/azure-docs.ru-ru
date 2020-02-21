---
title: Настройка защищенных веб-API приложений | Службы
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
ms.openlocfilehash: b05eefb2a0e516772390f898c22e723b08973338
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484457"
---
# <a name="protected-web-api-code-configuration"></a>Защищенный веб-API: конфигурация кода

Чтобы настроить код для защищенного веб-API, необходимо понимать следующее:

- Определяет интерфейсы API как защищенные.
- Настройка токена носителя.
- Проверка маркера.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Что определяет интерфейсы API ASP.NET и ASP.NET Core как защищенные?

Как и веб-приложения, веб-API ASP.NET и ASP.NET Core защищены, так как их действия контроллера начинаются с атрибута **[авторизовать]** . Действия контроллера могут вызываться только в том случае, если API вызывается с разрешенным удостоверением.

Учитывайте следующие вопросы.

- Только приложение может вызывать веб-API. Как API знает удостоверение приложения, которое его вызывает?
- Если приложение вызывает API от имени пользователя, каково удостоверение пользователя?

## <a name="bearer-token"></a>Маркер носителя

Токен носителя, заданный в заголовке при вызове приложения, содержит сведения об удостоверении приложения. Он также содержит сведения о пользователе, если веб-приложение не принимает вызовы между службами из управляющего приложения.

Ниже приведен пример C# кода, в котором показан клиент, вызывающий API после получения маркера с помощью библиотеки проверки подлинности Майкрософт для .net (MSAL.NET):

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Клиентское приложение запрашивает токен носителя в конечной точке платформы идентификации Майкрософт *для веб-API*. Веб-API — это единственное приложение, которое должно проверять маркер и просматривать содержащиеся в нем утверждения. Клиентские приложения никогда не должны пытаться проверить утверждения в токенах.
>
> В будущем для веб-API может потребоваться шифрование маркера. Это требование помешает доступу к клиентским приложениям, которые могут просматривать маркеры доступа.

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

При вызове приложения в действии контроллера, которое содержит атрибут **[Authorization]** , ASP.NET и ASP.NET Core извлекают маркер доступа из токена носителя заголовка авторизации. Затем маркер доступа перенаправляется в по промежуточного слоя JwtBearer, который вызывает расширения Microsoft IdentityModel для .NET.

В ASP.NET Core это по промежуточного слоя инициализируется в файле Startup.cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

По промежуточного слоя добавляется в веб-API с помощью следующей инструкции:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 В настоящее время шаблоны ASP.NET Core создают веб-интерфейсы API Azure Active Directory (Azure AD), которые входят в состав пользователей вашей организации или в любой организации. Они не входят в систему пользователей с личными учетными записями. Но вы можете изменить шаблоны, чтобы использовать конечную точку платформы идентификации Майкрософт, добавив следующий код в Startup.cs:

```csharp
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

Предыдущий фрагмент кода извлекается из пошагового руководства по ASP.NET Core веб-API в [Microsoft. Identity. Web/вебаписервицеколлектионекстенсионс. CS # L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). Метод **аддпротектедвебапи** , который выполняет больше, чем показывает фрагмент, вызывается из Startup.cs.

## <a name="token-validation"></a>Проверка маркеров

В предыдущем фрагменте по промежуточного слоя JwtBearer (например, по промежуточного слоя OpenID Connect Connect в веб-приложениях) проверяет маркер на основе значения `TokenValidationParameters`. Маркер расшифровывается по мере необходимости, извлекаются утверждения и проверяется подпись. По промежуточного слоя затем проверяет маркер, проверяя эти данные:

- Аудитория: маркер предназначен для веб-API.
- IT: она была выдана для приложения, которому разрешено вызывать веб-API.
- Издатель: он был выдан доверенной службой маркеров безопасности (STS).
- Срок действия: время существования в диапазоне.
- Сигнатура: она не была изменена.

Также могут быть специальные проверки. Например, можно проверить, что ключи подписывания, внедренные в маркер, являются доверенными и что этот маркер не воспроизводится. Наконец, для некоторых протоколов требуются определенные проверки.

### <a name="validators"></a>Проверяющие элементы управления

Шаги проверки фиксируются в проверяющих элементах управления, предоставляемых библиотеками [Microsoft IdentityModel Extensions для .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) с открытым исходным кодом. Проверяющие элементы управления определяются в исходном файле библиотеки [Microsoft. IdentityModel. Tokens/проверяющих элементов. CS](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

В следующей таблице описаны проверяющие элементы управления.

| Управления | Description |
|---------|---------|
| **валидатеаудиенце** | Гарантирует, что маркер предназначен для приложения, которое проверяет маркер. |
| **ValidateIssuer** | Гарантирует, что маркер был выдан доверенной службой STS, то есть от доверенного лица. |
| **валидатеиссуерсигнингкэй** | Гарантирует, что приложение, проверяющее маркер, доверяет ключу, который использовался для подписания маркера. Существует особый случай, когда ключ внедряется в маркер. Но в этом случае обычно не возникает. |
| **валидателифетиме** | Гарантирует, что маркер все еще или уже является допустимым. Проверяющий элемент управления проверяет, находится ли время существования маркера в диапазоне, указанном в **NotBefore** и **истечении срока действия** утверждений. |
| **валидатесигнатуре** | Гарантирует, что маркер не был изменен. |
| **валидатетокенреплай** | Гарантирует, что маркер не воспроизводится. Существует особый случай использования некоторых протоколов OneTime. |

Проверяющие элементы управления связаны со свойствами класса **TokenValidationParameters** . Свойства инициализируются из конфигурации ASP.NET и ASP.NET Core.

В большинстве случаев изменять параметры не требуется. Приложения, не являющиеся отдельными клиентами, являются исключениями. Эти веб-приложения принимают пользователей из любой организации или из личных учетных записей Майкрософт. Издатели в этом случае должны быть проверены.

## <a name="token-validation-in-azure-functions"></a>Проверка токенов в функциях Azure

Вы также можете проверить входящие маркеры доступа в функциях Azure. Примеры такой проверки можно найти в [Microsoft .NET](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)и [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Проверка областей и ролей приложений в коде](scenario-protected-web-api-verification-scope-app-roles.md)
