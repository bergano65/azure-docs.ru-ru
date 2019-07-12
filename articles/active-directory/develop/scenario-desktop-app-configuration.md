---
title: Классическое приложение, что вызовы веб-API (Настройка кода) — платформе Microsoft identity
description: Научитесь создавать приложения для настольных систем, вызовы веб-API-интерфейсы (конфигурации кода приложения)
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
ms.openlocfilehash: 600b6db1eb3d3b422d62e49c5bc816a1a56370f9
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798510"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Приложения на рабочем столе, вызовы веб-интерфейсы API — код конфигурации

Теперь, когда вы создали приложение, вы узнаете, как настроить код с координатами приложения.

## <a name="msal-libraries"></a>Библиотеки MSAL

Только библиотека MSAL, поддержка приложений для настольных компьютеров сегодня является MSAL.NET

## <a name="public-client-application"></a>Общедоступного клиентского приложения

С точки зрения кода классических приложений — это открытый клиентские приложения, и вот почему вы создадите и манипулировать MSAL.NET `IPublicClientApplication`. Еще раз действия будет немного отличается ли вы использовать интерактивную проверку подлинности или нет.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Исключительно по коду

Следующий код создает экземпляр общедоступного клиентского приложения, вход в систему пользователей в общедоступном облаке Microsoft Azure, с помощью рабочую и учебную учетную запись либо личную учетную запись Майкрософт.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Если вы планируете использовать интерактивную проверку подлинности или устройство потока кода, как показано выше, вы хотите использовать `.WithRedirectUri` модификатор:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>С помощью файлов конфигурации

Следующий код создает экземпляр общедоступного клиентского приложения из объекта конфигурации, который может быть заполнены по умолчанию программным способом или чтение из файла конфигурации

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Более сложные конфигурации

Приложения, построение, добавив несколько модификаторов можно подробнее. Например если требуется, чтобы приложения следует мультитенантное приложение в национальном облаке (здесь для государственных организаций США), можно написать так:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET также содержит модификатор для 2019 служб федерации Active Directory:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Наконец Если вы хотите получать маркеры для клиента Azure AD B2C, можно указать вашего клиента, как показано в следующем фрагменте кода:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Подробнее

Дополнительные сведения о том, как настроить приложение для рабочего стола MSAL.NET:

- Список доступных на все модификаторы `PublicClientApplicationBuilder`, см. в справочной документации [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Описание всех параметров, отображенных в `PublicClientApplicationOptions` см. в разделе [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), справочника

## <a name="complete-example-with-configuration-options"></a>Полный пример с параметрами конфигурации

Представьте себе консольное приложение .NET Core, которое содержит следующие `appsettings.json` файла конфигурации:

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

У вас есть небольшой код для чтения файла с помощью .NET, предоставленные framework конфигурации;

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

Теперь чтобы создать приложение, просто необходимо написать следующий код:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

и перед вызовом `.Build()` метод, можно переопределить конфигурацию с вызовами `.WithXXX` методы, как показано ранее.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Получение токена для классического приложения](scenario-desktop-acquire-token.md)
