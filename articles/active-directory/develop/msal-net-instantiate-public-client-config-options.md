---
title: Создание экземпляра общедоступного клиентского приложения с параметрами (Библиотека проверки подлинности Microsoft для .NET) | Службы
description: Узнайте, как создать общедоступное клиентское приложение с параметрами конфигурации с помощью библиотеки проверки подлинности Microsoft для .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1311e047b63cc9b5cccc785fbcd118db29f7c4bd
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532618"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Создание экземпляра общедоступного клиентского приложения с параметрами конфигурации с помощью MSAL.NET

В этой статье описывается создание экземпляра общедоступного [клиентского приложения](msal-client-applications.md) с помощью библиотеки проверки подлинности Майкрософт для .net (MSAL.NET).  Экземпляр приложения создается с параметрами конфигурации, определенными в файле параметров.

Перед инициализацией приложения необходимо сначала [зарегистрировать](quickstart-register-app.md) его, чтобы приложение можно было интегрировать с платформой Microsoft Identity. После регистрации может потребоваться следующая информация (которую можно найти в портал Azure):

- Идентификатор клиента (строка, представляющая GUID)
- URL-адрес поставщика удостоверений (с именем экземпляра) и аудитория входа для приложения. Эти два параметра вместе называются полномочиями.
- Идентификатор клиента, если вы пишете бизнес-приложение исключительно для вашей организации (также называется одноклиентским приложением).
- Для веб-приложений и иногда для общедоступных клиентских приложений (в частности, если приложению требуется использовать брокер) вы также настроили redirectUri, где поставщик удостоверений свяжется с приложением с маркерами безопасности.


Консольное приложение .NET Core может иметь следующий файл конфигурации *appSettings. JSON* :

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

Следующий код считывает этот файл с помощью платформы конфигурации .NET:

```csharp
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
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

Следующий код создает приложение, используя конфигурацию из файла параметров:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

