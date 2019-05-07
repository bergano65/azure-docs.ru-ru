---
title: Создать экземпляр общедоступного клиентского приложения с параметрами (библиотека проверки подлинности Майкрософт для .NET) | Azure
description: Узнайте, как создать экземпляр общедоступного клиентского приложения с параметрами конфигурации, с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a483cd6e22ce3c9fafb9b2b4d839e22c248f020
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158705"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Создать экземпляр общедоступного клиентского приложения с параметрами конфигурации, с помощью MSAL.NET

В этой статье описывается, как создать экземпляр [общедоступного клиентского приложения](msal-client-applications.md) с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).  Приложение создается с помощью параметров конфигурации, определенные в файле параметров.

Перед инициализацией приложения, необходимо сначала [зарегистрировать](quickstart-register-app.md) его таким образом, чтобы приложение можно интегрировать с платформой Microsoft identity. После регистрации может потребоваться следующие сведения (которые можно найти на портале Azure):

- Идентификатор клиента (строка, представляющая GUID)
- URL-адрес поставщика удостоверений (с именем экземпляра) и аудитории входа для приложения. Эти два параметра, называются полномочия.
- Идентификатор клиента, при создании строки бизнес-приложений исключительно для вашей организации (также именованного приложения одним клиентом).
- Для веб-приложений и иногда для общедоступных клиентских приложений (в частности, если ваше приложение должно использовать брокер) будет также установки redirectUri где поставщика удостоверений будет обращаться к задней приложения с помощью маркеров безопасности.


Использовать следующее консольное приложение .NET Core *appsettings.json* файла конфигурации:

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

Следующий код считывает этот файл с помощью конфигурации .NET framework:

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

В следующем коде создается приложение, используя конфигурацию из файла параметров:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

