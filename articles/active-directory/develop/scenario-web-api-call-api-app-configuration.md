---
title: Веб-API, который вызывает нисходящие веб-API (конфигурация кода приложения) — платформа Microsoft Identity
description: Узнайте, как создать веб-API, который вызывает веб-API (конфигурация кода приложения).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bae9f565dd37fbd3bcae38833662e13e0b7ac6d
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960651"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Веб-API, вызывающий веб-API — конфигурация кода

После регистрации веб-API можно настроить код для приложения.

Код для настройки веб-API таким образом, чтобы он вызывал нисходящие веб-API, построенные поверх кода, используемого для защиты веб-API. Дополнительные сведения см. в разделе [защищенный веб-API — Конфигурация приложения](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Код подписан на Онтокенвалидатед

На основе конфигурации кода для любых защищенных веб-API необходимо подписываться на проверку токена носителя, полученного при вызове API:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform (a.k.k AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it 
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Поток On-Behalf-Of

Метод Аддаккаунттокачефромжвт () должен:

- Создайте экземпляр MSAL конфиденциального клиентского приложения.
- Вызовите `AcquireTokenOnBehalf` для обмена токеном носителя, который был получен клиентом для веб-API, от токена носителя для того же пользователя, но для нашего API, чтобы вызвать нисходящий API.

### <a name="instantiate-a-confidential-client-application"></a>Создание экземпляра конфиденциального клиентского приложения

Этот поток доступен только в конфиденциальном потоке клиента, поэтому защищенный веб-API предоставляет учетные данные клиента (секрет клиента или сертификат) [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) через методы `WithClientSecret` или `WithCertificate`.

![изображение](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

Наконец, вместо секрета клиента или сертификата конфиденциальные клиентские приложения также могут доказать свою личность с помощью утверждений клиента.
Этот расширенный сценарий подробно описан в [утверждениях клиента](msal-net-client-assertions.md) .

### <a name="how-to-call-on-behalf-of"></a>Как позвонить от имени

Вызов от имени пользователя (OBO) осуществляется путем вызова метода [аккуиретокенонбехалф](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) в интерфейсе `IConfidentialClientApplication`.

`UserAssertion` создается на основе токена носителя, полученного веб-API от собственных клиентов. Существует [два конструктора](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), один из которых принимает маркер носителя JWT, и один, принимающий любой тип пользовательского утверждения (другой тип маркера безопасности, который затем указывается в дополнительном параметре с именем `assertionType`).

![изображение](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

На практике поток OBO часто используется для получения маркера для нисходящих API и сохранения его в кэше пользовательских маркеров MSAL.NET, чтобы другие части веб-API могли впоследствии вызывать [переопределения](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) ``AcquireTokenOnSilent`` для вызова нижестоящих API. Этот вызов оказывает воздействие обновления токенов при необходимости.

```CSharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```

Вы также можете просмотреть пример реализации потока в [NodeJS и функциях Azure](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Протокол

Дополнительные сведения о протоколе "от имени пользователя" см. в [статье о платформе Microsoft Identity и OAuth 2,0 от имени потока](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Получение маркера для приложения](scenario-web-api-call-api-acquire-token.md)
