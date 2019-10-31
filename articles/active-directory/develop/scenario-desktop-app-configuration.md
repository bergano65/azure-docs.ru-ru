---
title: Классическое приложение, вызывающее веб-API (конфигурация кода) — платформа Microsoft Identity
description: Узнайте, как создать классическое приложение, вызывающее веб-API (конфигурация кода приложения).
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6baf7d21748b5b524745f26302e70612dab29a8d
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175436"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Классическое приложение, вызывающее веб-API — конфигурация кода

Теперь, когда вы создали приложение, вы узнаете, как настроить код с помощью координат приложения.

## <a name="msal-libraries"></a>Библиотеки MSAL

Библиотеки Майкрософт, поддерживающие классические приложения:

  Библиотека MSAL | Описание
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Поддерживает создание классических приложений на нескольких платформах — Linux, Windows и MacOS
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Поддерживает создание классических приложений на нескольких платформах. Выполняется разработка — в общедоступной предварительной версии
  ![Java:](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Поддерживает создание классических приложений на нескольких платформах. Выполняется разработка — в общедоступной предварительной версии
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Поддерживает классические приложения, работающие только на macOS

## <a name="public-client-application"></a>Общедоступное клиентское приложение

С точки зрения кода, классические приложения являются общедоступными клиентскими приложениями. Конфигурация будет немного отличаться в зависимости от того, используется ли Интерактивная проверка подлинности.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Вам потребуется создать MSAL.NET `IPublicClientApplication`и управлять ими.

![ипубликклиентаппликатион](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Исключительно в коде

Следующий код создает экземпляр общедоступного клиентского приложения, пользователей для входа в Microsoft Azure общедоступное облако с рабочей или учебной учетной записью или персональным учетная запись Майкрософт.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Если вы планируете использовать интерактивную проверку подлинности или поток кода устройства, как показано выше, вы хотите использовать модификатор `.WithRedirectUri`:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>Использование файлов конфигурации

Следующий код создает экземпляр общедоступного клиентского приложения на основе объекта конфигурации, который может быть заполнен программно или прочитан из файла конфигурации.

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Более подробная конфигурация

Вы можете развить сборку приложений, добавив несколько модификаторов. Например, если вы хотите, чтобы приложение было многопользовательским приложением в национальном облаке (здесь правительство США), можно написать:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET также содержит модификатор для ADFS 2019:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Наконец, если требуется получить токены для клиента Azure AD B2C, можно указать клиент, как показано в следующем фрагменте кода:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Дополнительные сведения

Дополнительные сведения о настройке классического приложения MSAL.NET:

- Список всех модификаторов, доступных в `PublicClientApplicationBuilder`, см. в справочной документации [публикклиентаппликатионбуилдер](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Описание всех параметров, предоставляемых в `PublicClientApplicationOptions` см. в справочной документации по [публикклиентаппликатионоптионс](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions).

### <a name="complete-example-with-configuration-options"></a>Полный пример с параметрами конфигурации

Представьте себе консольное приложение .NET Core, которое имеет следующий `appsettings.json` файл конфигурации:

```JSon
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

У вас есть немало кода для чтения этого файла с помощью платформы конфигурации, предоставленной .NET.

```CSharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
 /// in Microsoft Azure public clouds or national / sovereign clouds
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a json file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint config
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Теперь, чтобы создать приложение, нужно просто написать следующий код:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

перед вызовом метода `.Build()` можно переопределить конфигурацию с помощью вызовов методов `.WithXXX`, как показано выше.

# <a name="javatabjava"></a>[Java](#tab/java)

Ниже приведен класс, используемый в примерах разработки Java MSAL для настройки примеров: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_COMMON)
        .build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

Следующий код создает экземпляр общедоступного клиентского приложения, пользователей для входа в Microsoft Azure общедоступное облако с рабочей или учебной учетной записью или персональным учетная запись Майкрософт.

### <a name="quick-configuration"></a>Быстрая настройка

Objective-C.

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Более подробная конфигурация

Вы можете развить сборку приложений, добавив несколько модификаторов. Например, если вы хотите, чтобы приложение было многопользовательским приложением в национальном облаке (здесь правительство США), можно написать:

Objective-C.

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Получение маркера для классического приложения](scenario-desktop-acquire-token.md)
