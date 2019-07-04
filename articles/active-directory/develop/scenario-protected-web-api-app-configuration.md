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
ms.openlocfilehash: fa262c1c6a091575a70c5670b1c7a7c96e8e2128
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536895"
---
# <a name="protected-web-api-code-configuration"></a>Защищенный веб-API: Конфигурация кода

Чтобы настроить код для защищенных веб-API, необходимо понять то, что определяет интерфейсы API, как защищенный, как настроить токен носителя и способы проверки маркера.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>То, что определяет основные ASP.NET/ASP.NET API-интерфейсы, как защищенный?

Веб-приложений, ASP.NET/ASP.NET Core веб-API «защищены», так как их действий контроллера имеют префикс `[Authorize]` атрибута. Таким образом действия контроллера могут вызываться только в том случае, если вызов API с удостоверением, которая имеет права.

Ответьте на следующие вопросы:

- Как веб-API, что идентификатор приложения, который ее вызывает? (Только приложения можно назвать веб-API).
- Если приложение называется веб-API от имени пользователя, что такое удостоверение пользователя?

## <a name="bearer-token"></a>Маркер носителя

Сведения об удостоверении приложения и пользователь (если веб-приложение принимает вызовы service to service из приложения управляющей программы), хранящиеся в маркер носителя, который задается в заголовке, при вызове приложения.

Вот C# пример кода, показывающий клиента, вызывающего API, после его получает маркер, с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET):

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
> Токен носителя была запрошена клиентским приложением к конечной точке платформы удостоверений Microsoft *для веб-API*. Веб-API — это единственное приложение должно проверить токен и просмотреть утверждения, содержащиеся в ней. Клиентские приложения никогда не попробуйте изучить утверждения в маркерах. (Веб-API может потребовать, в будущем, шифрование маркера. Это требование может предотвратить доступ для клиентских приложений, которые могут просматривать маркеры доступа.)

## <a name="jwtbearer-configuration"></a>JwtBearer конфигурации

В этом разделе описываются способы настройки токена носителя.

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

### <a name="code-initialization"></a>Код инициализации

При вызове приложения на действия контроллера, который содержит `[Authorize]` атрибут ASP.NET/ASP.NET Core проверяет токен носителя в заголовке авторизации запроса, вызывающего и извлекает маркер доступа. Токен перенаправляется по промежуточного слоя JwtBearer, который вызывает IdentityModel расширения Майкрософт для .NET.

В ASP.NET Core это по промежуточного слоя инициализируется в файле Startup.cs:

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Данная инструкция по промежуточного слоя добавляется веб-API:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 В настоящее время шаблоны ASP.NET Core создать Azure Active Directory (Azure AD) с веб-API, войдите в пользователей в вашей организации или любой организации, а не с личными учетными записями. Но вы легко можете изменить их на использование конечной точки платформы Microsoft identity, добавив следующий код в файле Startup.cs:

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API.
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
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Проверка маркеров

По промежуточного слоя JwtBearer, такие как по промежуточного слоя OpenID Connect в веб-приложениях, направляется по `TokenValidationParameters` для проверки токена. Расшифровки маркера (при необходимости), извлекаются утверждения и выполняется проверка сигнатуры. Затем, по промежуточного слоя проверяет маркер, установив для этих данных:

- Она предназначена для веб-API (аудитории).
- Он выдан для приложения, которое могут вызывать веб-API (sub).
- Он выдан Служба токенов безопасности (STS) (издатель).
- Его времени существования находится в диапазоне (срок действия).
- Он не был подделан (подпись).

Также может быть необязательно. Например это можно проверить, что ключи подписывания (при его внедрении в токене) являются доверенными и не воспроизводится маркер. Наконец некоторые протоколы требуют определенных проверок.

### <a name="validators"></a>Проверяющие элементы управления

Шаги проверки регистрируются в проверяющих элементов управления, которые входят в [IdentityModel расширения Майкрософт для .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) библиотеки с открытым исходным кодом, в одном исходном файле: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Проверяющие элементы управления, описанных в этой таблице:

| Проверяющий элемент управления | Описание |
|---------|---------|
| `ValidateAudience` | Гарантирует, что маркер является для приложения, которое проверяет токен (для me). |
| `ValidateIssuer` | Гарантирует, что маркер был выдан службой доверенных маркеров безопасности (от кого-то, которое я доверяю). |
| `ValidateIssuerSigningKey` | Гарантирует ключ, который использовался для подписи маркера приложения, проверка маркера отношения доверия. (Особый случай, где ключ внедряется в маркере. Обычно не требуются.) |
| `ValidateLifetime` | Гарантирует, что маркер является допустимым по-прежнему (или ранее). Проверяющий элемент управления проверяет, если время существования маркера (`notbefore` и `expires` утверждений) находится в диапазоне. |
| `ValidateSignature` | Гарантирует, что токен не было подделано. |
| `ValidateTokenReplay` | Гарантирует, что токен не воспроизводятся. (Особый случай для некоторых протоколов onetime использования). |

Проверяющие элементы управления все связанные со свойствами объекта `TokenValidationParameters` класса, сами инициализирован из конфигурации ASP.NET/ASP.NET Core. В большинстве случаев не придется изменять параметры. Есть одно исключение, для приложений, которые не являются одним клиентом. (То есть веб-приложения, поддерживающие пользователей из любой организации или личных учетных записей Майкрософт.) В этом случае необходимо проверить издателя.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Убедитесь, областей и ролей приложения в коде](scenario-protected-web-api-verification-scope-app-roles.md)
