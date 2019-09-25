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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0926e6800dbcd81d2e542e27afe3afb1240cff22
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268419"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Классическое приложение, вызывающее веб-API — конфигурация кода

Теперь, когда вы создали приложение, вы узнаете, как настроить код с помощью координат приложения.

## <a name="msal-libraries"></a>Библиотеки MSAL

Единственной библиотекой MSAL, поддерживающей классические приложения на нескольких платформах, сегодня является MSAL.NET.

MSAL для iOS и macOS поддерживает классические приложения, работающие только на macOS. 

## <a name="public-client-application-with-msalnet"></a>Общедоступное клиентское приложение с MSAL.NET

С точки зрения кода, классические приложения являются общедоступными клиентскими приложениями, поэтому вы будете создавать MSAL.NET `IPublicClientApplication`и управлять ими. При использовании интерактивной проверки подлинности все еще будет немного отличаться.

![ипубликклиентаппликатион](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Исключительно в коде

Следующий код создает экземпляр общедоступного клиентского приложения, пользователей для входа в Microsoft Azure общедоступное облако с рабочей или учебной учетной записью или персональным учетная запись Майкрософт.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Если вы планируете использовать интерактивную проверку подлинности или поток кода устройства, как показано выше, необходимо `.WithRedirectUri` использовать модификатор:

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

### <a name="learn-more"></a>Подробнее...

Дополнительные сведения о настройке классического приложения MSAL.NET:

- Список всех модификаторов, доступных в `PublicClientApplicationBuilder`, см. в справочной документации [публикклиентаппликатионбуилдер](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Описание всех параметров, предоставляемых в `PublicClientApplicationOptions` разделе см. [публикклиентаппликатионоптионс](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), в справочной документации.

## <a name="complete-example-with-configuration-options"></a>Полный пример с параметрами конфигурации

Представьте себе консольное приложение .NET Core, которое имеет `appsettings.json` следующий файл конфигурации:

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

перед вызовом `.Build()` метода можно переопределить конфигурацию с помощью `.WithXXX` вызовов методов, как показано выше.

## <a name="public-client-application-with-msal-for-ios-and-macos"></a>Общедоступное клиентское приложение с MSAL для iOS и macOS

Следующий код создает экземпляр общедоступного клиентского приложения, пользователей для входа в Microsoft Azure общедоступное облако с рабочей или учебной учетной записью или персональным учетная запись Майкрософт.

### <a name="quick-configuration"></a>Быстрая настройка

Objective-C.

```objc
NSError *msalError = nil;
    
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

SWIFT
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

SWIFT

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Получение маркера для классического приложения](scenario-desktop-acquire-token.md)
