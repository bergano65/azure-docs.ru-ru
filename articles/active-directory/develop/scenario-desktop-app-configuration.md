---
title: Настройка классических приложений, вызывающих веб-API — платформа Microsoft Identity | Службы
description: Узнайте, как настроить код для классического приложения, вызывающего веб-API
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, tracking-python
ms.openlocfilehash: f197dd5063f8584968277d8d55298c03d9d71ea6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84558846"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Классическое приложение, вызывающее веб-API: конфигурация кода

Теперь, когда вы создали приложение, вы узнаете, как настроить код с помощью координат приложения.

## <a name="microsoft-authentication-libraries"></a>Библиотеки проверки подлинности Майкрософт

Следующие библиотеки проверки подлинности Майкрософт (Мсалс) поддерживают классические приложения.

  Библиотека проверки подлинности Майкрософт | Описание
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Поддерживает создание классических приложений на нескольких платформах, таких как Linux, Windows и macOS.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Поддерживает создание классических приложений на нескольких платформах.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Поддерживает создание классических приложений на нескольких платформах.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Поддерживает классические приложения, работающие только на macOS.

## <a name="public-client-application"></a>Общедоступное клиентское приложение

С точки зрения кода, классические приложения являются общедоступными клиентскими приложениями. Конфигурация будет немного отличаться в зависимости от того, используется ли Интерактивная проверка подлинности.

# <a name="net"></a>[.NET](#tab/dotnet)

Вам потребуется создать MSAL.NET и управлять им `IPublicClientApplication` .

![ипубликклиентаппликатион](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Исключительно в коде

Следующий код создает открытое клиентское приложение и выполняет вход пользователей в Microsoft Azure общедоступное облако с рабочей или учебной учетной записью или личным учетная запись Майкрософт.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Если вы планируете использовать интерактивную проверку подлинности или поток кода устройства, как показано выше, используйте `.WithRedirectUri` модификатор.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Использование файлов конфигурации

Следующий код создает экземпляр общедоступного клиентского приложения на основе объекта конфигурации, который может быть заполнен программно или прочитан из файла конфигурации.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Более подробная конфигурация

Вы можете развить сборку приложений, добавив несколько модификаторов. Например, если вы хотите, чтобы приложение было многоклиентским приложением в национальной облаке, например, в этом примере, мы можем написать следующее:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET также содержит модификатор для службы федерации Active Directory (AD FS) 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Наконец, если вы хотите получить токены для клиента B2C (Azure AD) в Azure Active Directory, укажите клиент, как показано в следующем фрагменте кода:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Подробнее

Дополнительные сведения о настройке классического приложения MSAL.NET:

- Список всех модификаторов, доступных в `PublicClientApplicationBuilder` , см. в справочной документации [публикклиентаппликатионбуилдер](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Описание всех параметров, предоставляемых в `PublicClientApplicationOptions` , см. в разделе [публикклиентаппликатионоптионс](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) в справочной документации.

### <a name="complete-example-with-configuration-options"></a>Полный пример с параметрами конфигурации

Представьте себе консольное приложение .NET Core, которое имеет следующий `appsettings.json` файл конфигурации:

```json
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

У вас есть немало кода для чтения в этом файле с помощью. Инфраструктура конфигурации, предоставленная NET:

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
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

  // Read the auth and graph endpoint configuration
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

Теперь, чтобы создать приложение, напишите следующий код:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Перед вызовом `.Build()` метода можно переопределить конфигурацию с помощью вызовов `.WithXXX` методов, как показано выше.

# <a name="java"></a>[Java](#tab/java)

Ниже приведен класс, используемый в примерах разработки Java MSAL для настройки примеров: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macos"></a>[MacOS](#tab/macOS)

Следующий код создает открытое клиентское приложение и выполняет вход пользователей в Microsoft Azure общедоступное облако с рабочей или учебной учетной записью или личным учетная запись Майкрософт.

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

Вы можете развить сборку приложений, добавив несколько модификаторов. Например, если вы хотите, чтобы приложение было многоклиентским приложением в национальной облаке, например, в этом примере, мы можем написать следующее:

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
