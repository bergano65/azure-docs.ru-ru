---
title: Настройка защищенных веб-приложений API Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как создать защищенный веб-aPI и настроить код приложения.
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
ms.openlocfilehash: 3f07105c14d4dafeb689eaaf7d679f93e5f235fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262520"
---
# <a name="protected-web-api-code-configuration"></a>Защищенный веб-aPI: конфигурация кода

Чтобы настроить код для защищенного веб-API, необходимо понять:

- Что определяет AIS как защищенные.
- Как настроить маркер носителя.
- Как проверить токен.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Что определяет ASP.NET и ASP.NET основные AIS как защищенные?

Как и веб-приложения, ASP.NET и ASP.NET основные web-aIs защищены, потому что их действия контроллера прикреплятся к атрибуту **«Authorize».** Действия контроллера могут быть вызваны только в том случае, если API вызывается с авторизованным удостоверением личности.

Ответьте на следующие вопросы.

- Только приложение может вызвать веб-API. Как API узнает личность приложения, которое его вызывает?
- Если приложение вызывает API от имени пользователя, какова личность пользователя?

## <a name="bearer-token"></a>Маркер носителя

Токен носителя, установленный в заголовке при вызове приложения, содержит информацию об итоге приложения. Он также содержит информацию о пользователе, если веб-приложение не принимает звонки от службы в службу от приложения daemon.

Вот пример кода C', на котором клиент вызывает API после приобретения токена в библиотеке подлинности Майкрософт для .NET (MSAL.NET):

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
> Клиентское приложение запрашивает токен носителя в конечную точку платформы Майкрософт *для Web API.* Веб-API является единственным приложением, которое должно проверить токен и просмотреть содержащиеся в нем утверждения. Клиентские приложения никогда не должны пытаться проверять претензии в токенах.
>
> В будущем веб-API может потребовать шифрования токена. Это требование предотвратит доступ к клиентским приложениям, которые могут просматривать токены доступа.

## <a name="jwtbearer-configuration"></a>Конфигурация JwtBearer

В этом разделе описывается, как настроить маркер носителя.

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

Когда приложение вызывается на действие контроллера, вмещает атрибут **«Authorize»,** ASP.NET и ASP.NET Core извлекают токен доступа из маркера напредъявителя заголовка авторизации. Токен доступа затем перенаправляется в промежуточное программное обеспечение JwtBearer, которое вызывает расширения Microsoft IdentityModel для .NET.

В ASP.NET Core это промежуточное программное обеспечение инициализировано в Startup.cs файле.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Промежуточное программное обеспечение добавляется в Web API этой инструкцией:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 В настоящее время шаблоны ASP.NET Core создают web-аПЫ Azure Active Directory (Azure AD), которые подписываются в пользователях в вашей организации или любой организации. Они не вписываются в пользователей с личными учетными записями. Но вы можете изменить шаблоны, чтобы использовать конечную точку платформы microsoft, добавив этот код в Startup.cs:

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

Предыдущий фрагмент кода извлекается из ASP.NET core web API дополнительный учебник в [Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs-L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). Метод **AddProtectedWebApi,** который делает больше, чем показывает фрагмент, вызывается из Startup.cs.

## <a name="token-validation"></a>Проверка маркеров

В предыдущем фрагменте, JwtBearer промежуточное программное обеспечение, как OpenID Connect промежуточного программного `TokenValidationParameters`обеспечения в веб-приложений, проверяет маркер на основе значения . Токен расшифровывается по мере необходимости, требования извлекаются, и подпись проверяется. Затем промежуточные программы проверяют токен, проверяя эти данные:

- Аудитория: Токен предназначен для веб-API.
- Sub: Он был выпущен для приложения, которое разрешено вызывать веб-API.
- Эмитент: Он был выдан надежной службой токенов безопасности (STS).
- Срок действия: Срок службы находится в диапазоне.
- Подпись: Это не было подделано.

Также могут быть специальные проверки. Например, можно проверить, что клавиши подписи при встраивании в маркер доверяют и что маркер не воспроизводится. Наконец, некоторые протоколы требуют конкретных проверки.

### <a name="validators"></a>Проверяющие элементы управления

Шаги проверки фиксируются в валидаторах, которые предоставляются [расширениями Microsoft IdentityModel для](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) библиотеки с открытым исходным кодом .NET. Валидаторы определены в файле исходного файла библиотеки [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

В этой таблице описаны валидаторы:

|  Validator | Описание |
|---------|---------|
| **Проверкааудитории** | Гарантирует, что токен предназначен для приложения, которое проверяет токен для вас. |
| **ПроверкаСизатор** | Гарантирует, что токен был выпущен доверенным STS, то есть это от кого-то, кому вы доверяете. |
| **ValidateIssuerSigningKey** | Гарантирует, что приложение, удостоверяющее проверку токена, доверяет ключу, который использовался для подписания токена. Есть особый случай, когда ключ встроен в токен. Но этот случай обычно не возникает. |
| **ПроверкаПожизненная** | Гарантирует, что токен по-прежнему или уже действителен. Валидатор проверяет, находится ли срок службы токена в диапазоне, указанном требованиями **неранее** и истекает срок **действия.** |
| **ПроверкаПодпись** | Гарантирует, что токен не был подделан. |
| **ValidateTokenReplay** | Гарантирует, что токен не воспроизводится. Есть особый случай для каких-то одноразовых протоколов. |

Валидаторы связаны со свойствами класса **TokenValidationParameters.** Свойства инициализированы из конфигурации ASP.NET и ASP.NET Core.

В большинстве случаев не нужно менять параметры. Исключениями являются приложения, не отосвитые для разных наемных клиентов. Эти веб-приложения принимают пользователей из любой организации или из личных учетных записей Майкрософт. Эмитенты в этом случае должны быть проверены.

## <a name="token-validation-in-azure-functions"></a>Проверка токенов в функциях Azure

Можно также проверить входящие маркеры доступа в Azure Functions. Примеры такой проверки можно найти в [Microsoft .NET,](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)и [Python.](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Проверка областей и ролей приложений в коде](scenario-protected-web-api-verification-scope-app-roles.md)
