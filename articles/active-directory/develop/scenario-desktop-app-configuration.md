---
title: Настройка настольных приложений, которые вызывают веб-AI - платформа идентификации Майкрософт (ru) Azure
description: Узнайте, как настроить код настольного приложения, которое вызывает web-аПО
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e0b43f7563c9dfac6374590f6b081197536fe31e
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869012"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Настольное приложение, которое вызывает веб-AIS: конфигурация кода

Теперь, когда вы создали приложение, вы узнаете, как настроить код с координатами приложения.

## <a name="microsoft-authentication-libraries"></a>Библиотеки аутентификации Майкрософт

Следующие библиотеки аутентификации Майкрософт (MSALs) поддерживают настольные приложения.

  Библиотека проверки подлинности Майкрософт | Описание
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Поддерживает создание настольного приложения на нескольких платформах, таких как Linux, Windows и macOS.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Поддерживает создание настольного приложения на нескольких платформах.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Поддерживает создание настольного приложения на нескольких платформах.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Поддерживает настольные приложения, работающие только на macOS.

## <a name="public-client-application"></a>Публичное приложение клиента

С точки зрения кода настольные приложения являются общедоступными приложениями для клиентов. Конфигурация будет немного отличаться в зависимости от того, используете ли вы интерактивную аутентификацию или нет.

# <a name="net"></a>[.NET](#tab/dotnet)

Вам нужно будет строить и `IPublicClientApplication`манипулировать MSAL.NET .

![IPublicКлиентПриложение](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Исключительно по коду

Следующий код мгновенно выполняет публичное клиентское приложение и подписывается в общедоступном облаке Microsoft Azure с рабочей или школьной учетной записью или личной учетной записью Майкрософт.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Если вы собираетесь использовать интерактивную аутентификацию или `.WithRedirectUri` поток кода устройства, как видно ранее, используйте модификатор.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Использование файлов конфигурации

Следующий код мгновенно выполняет публичное клиентское приложение от объекта конфигурации, которое может быть заполнено программно или прочитано из файла конфигурации.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Более продуманная конфигурация

Вы можете разработать здание приложения, добавив ряд модификаторов. Например, если вы хотите, чтобы ваше приложение было мультитенантным приложением в национальном облаке, например, правительство США показано здесь, вы можете написать:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET также содержит модификатор для служб Active Directory Federation 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Наконец, если вы хотите приобрести токены для клиента B2C Active Directory (Azure AD), укажите арендатора, как показано в следующем фрагменте кода:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Дополнительные сведения

Чтобы узнать больше о том, как настроить MSAL.NET настольное приложение:

- Список всех модификаторов, доступных на `PublicClientApplicationBuilder`, см справочная документация [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Для описания всех вариантов, `PublicClientApplicationOptions`приведенных в , см. [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) в справочной документации.

### <a name="complete-example-with-configuration-options"></a>Полный пример с вариантами конфигурации

Представьте себе консольное приложение `appsettings.json` .NET Core, которое имеет следующий файл конфигурации:

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

У вас есть мало кода для чтения в этом файле с помощью . Платформа конфигурации, предоставленная NET:

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

Перед вызовом `.Build()` к методу можно переопределить конфигурацию с помощью вызовов к `.WithXXX` методам, как это было видно ранее.

# <a name="java"></a>[Java](#tab/java)

Вот класс, используемый в образцах разработки MSAL Java для настройки образцов: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

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

Следующий код мгновенно выполняет публичное клиентское приложение и подписывается в общедоступном облаке Microsoft Azure с рабочей или школьной учетной записью или личной учетной записью Майкрософт.

### <a name="quick-configuration"></a>Быстрая конфигурация

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

### <a name="more-elaborated-configuration"></a>Более продуманная конфигурация

Вы можете разработать здание приложения, добавив ряд модификаторов. Например, если вы хотите, чтобы ваше приложение было мультитенантным приложением в национальном облаке, например, правительство США показано здесь, вы можете написать:

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
> [Приобретение токена для настольного приложения](scenario-desktop-acquire-token.md)
