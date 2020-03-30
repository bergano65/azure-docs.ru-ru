---
title: Мгновенное приложение для публичных клиентов (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как мгновенно использовать публичное клиентское приложение с вариантами конфигурации с помощью библиотеки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1dd06e139f931bbf8554f05f05c5d9b9ccf200e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083595"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Мгновенное публичное клиентское приложение с вариантами конфигурации с использованием MSAL.NET

В этой статье описывается, как мгновенно использовать [публичное клиентское приложение](msal-client-applications.md) с помощью библиотеки подлинности Майкрософт для .NET (MSAL.NET).  Приложение мгновенно используется параметрами конфигурации, определенными в файле настроек.

Прежде чем инициализировать приложение, сначала необходимо [зарегистрировать](quickstart-register-app.md) его, чтобы ваше приложение было интегрировано с платформой идентификации Майкрософт. После регистрации вам может понадобиться следующая информация (которая может быть найдена на портале Azure):

- Идентификатор клиента (строка, представляющая GUID)
- URL-адрес поставщика идентификационных данных (названный экземпляр) и аудитория для вашего приложения. Эти два параметра коллективно известны как власть.
- Идентификатор клиента, если вы пишете строку бизнес-приложения исключительно для вашей организации (также названное приложением с одним арендатором).
- Для веб-приложений, а иногда и для приложений для публичных клиентов (в частности, когда вашему приложению необходимо использовать брокера), вы также установите redirectUri, где поставщик идентификационных данных свяжется с вашим приложением с маркерами безопасности.


Консольное приложение .NET Core может иметь следующий файл *конфигурации appsettings.json:*

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

Следующий код считывает этот файл с помощью рамочной конфигурации .NET:

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

Следующий код создает приложение, используя конфигурацию из файла настроек:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

