---
title: Создать экземпляр конфиденциального клиентского приложения с параметрами (библиотека проверки подлинности Майкрософт для .NET) | Azure
description: Узнайте, как создать экземпляр конфиденциального клиентского приложения с параметрами конфигурации, с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
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
ms.openlocfilehash: 6f935b1b2815501710444e3f921a157ba02e3215
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544087"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Создать экземпляр конфиденциального клиентского приложения с параметрами конфигурации, с помощью MSAL.NET

В этой статье описывается, как создать экземпляр [конфиденциального клиентского приложения](msal-client-applications.md) с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).  Приложение создается с помощью параметров конфигурации, определенные в файле параметров.

Перед инициализацией приложения, необходимо сначала [зарегистрировать](quickstart-register-app.md) его таким образом, чтобы приложение можно интегрировать с платформой Microsoft identity. После регистрации может потребоваться следующие сведения (которые можно найти на портале Azure):

- Идентификатор клиента (строка, представляющая GUID)
- URL-адрес поставщика удостоверений (с именем экземпляра) и аудитории входа для приложения. Эти два параметра, называются полномочия.
- Идентификатор клиента, при создании строки бизнес-приложений исключительно для вашей организации (также именованного приложения одним клиентом).
- Секрет приложения (строка секрета клиента) или сертификат (типа X509Certificate2) Если это приложение конфиденциального клиента.
- Для веб-приложений и иногда для общедоступных клиентских приложений (в частности, если ваше приложение должно использовать брокер) будет также установки redirectUri где поставщика удостоверений будет обращаться к задней приложения с помощью маркеров безопасности.

## <a name="configure-the-application-from-the-config-file"></a>Настройка приложения из файла конфигурации
Имя свойства параметра в MSAL.NET соответствовать имени свойства `AzureADOptions` в ASP.NET Core, поэтому не нужно писать связующий код.

Конфигурация приложений ASP.NET Core описан в *appsettings.json* файла:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

Начиная с версии MSAL.NET v3.x, можно настроить приложение конфиденциального клиента из файла конфигурации. Классы, связанные с конфигурацией приложений находятся в папке `Microsoft.Identity.Client.AppConfig` пространства имен.

В классе, который нужно настроить и создание экземпляра приложения, необходимо объявить `ConfidentialClientApplicationOptions` объекта.  Привязку к экземпляру из приложения параметров конфигурации, считанными из источника (включая файл appconfig.json):

```csharp
using Microsoft.Identity.Client.AppConfig;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Это позволяет располагать содержимое раздела «AzureAD» *appsettings.json* файл, который нужно привязать к соответствующим свойствам объекта `ConfidentialClientApplicationOptions` объекта.  Теперь создайте `ConfidentialClientApplication` объекта:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Добавить конфигурацию среды выполнения
В конфиденциального клиентского приложения у вас обычно есть кэш для каждого пользователя. Поэтому необходимо получить кэш, связанный с пользователем и оповещения сборщика приложения, что вы хотите использовать его. Таким же образом может потребоваться динамически вычисляемое URI перенаправления. В этом случае код выглядит так:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

