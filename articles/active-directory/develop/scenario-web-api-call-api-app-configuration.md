---
title: Веб-API, вызывающее подчиненными веб-API-интерфейсы (конфигурации кода приложения) — платформе Microsoft identity
description: Сведения о создании веб-API, что вызовы веб-API (конфигурации кода приложения)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dedef2d22df9c8c81410296bdb0c4814bd98b80
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507132"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Веб-API, что вызовы веб-интерфейсы API — код конфигурации

После регистрации веб-API, можно настроить код для приложения.

Код, чтобы настроить веб-API, таким образом, чтобы он вызывает веб-интерфейсы API нижнего уровня подключает код, используемый для проекта веб-API. Дополнительные сведения см. в разделе [защищенный веб-API — Конфигурация приложения](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Подписка на OnTokenValidated кода

На основе конфигурации кода для любого защищенного веб-API необходимо подписаться на проверку токена носителя, полученного при вызове API:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform v2.0 (AAD v2.0)
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

Метод AddAccountToCacheFromJwt() должен:

- Создать экземпляр MSAL конфиденциального клиента приложения.
- Вызовите `AcquireTokenOnBehalf` для обмена на маркер носителя, который был получен с помощью клиента для веб-API, от токен носителя для одного пользователя, но для нашего API для вызова API нижнего уровня.

### <a name="instantiate-a-confidential-client-application"></a>Создать экземпляр конфиденциального клиентского приложения

Этот поток доступен только в потоке конфиденциального клиента, защищенный веб-API предоставляет учетные данные клиента (секрет клиента или сертификат) [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.appconfig.confidentialclientapplicationbuilder?view=azure-dotnet-preview) через `WithClientSecret` или `WithCertificate`методы, соответственно.

![image](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

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

### <a name="how-to-call-on-behalf-of"></a>Как вызвать on-behalf-of

Вызов on-behalf-of (OBO) можно сделать, вызвав [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.apiconfig.acquiretokenonbehalfofparameterbuilder?view=azure-dotnet-preview) метод `IConfidentialClientApplication` интерфейс.

`ClientAssertion` Построена на основе токена носителя, полученных веб-API из своих клиентов. Существуют [два конструктора](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), одна принимает токена носителя JWT и с любого типа утверждения пользователя (другой вид маркера безопасности, какой тип задается в дополнительный параметр с именем `assertionType`).

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

На практике потока OBO часто используется для получения маркера для API нижнего уровня и сохранить его в кэше токенов пользователя MSAL.NET, таким образом, чтобы другие части веб-API может позднее вызвать на [переопределяет](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) из ``AcquireTokenOnSilent`` для вызова API подчиненных. Это приводит к обновление маркеров, при необходимости.

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

## <a name="protocol"></a>Протокол

Дополнительные сведения о протоколе on-behalf-of, см. в разделе [платформой Microsoft identity и flow OAuth 2.0 On-Behalf-Of](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Получение токена для приложения](scenario-web-api-call-api-acquire-token.md)
