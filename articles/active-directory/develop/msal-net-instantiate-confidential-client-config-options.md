---
title: Создание экземпляра конфиденциального клиентского приложения (MSAL.NET) | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как создать конфиденциальное клиентское приложение с параметрами конфигурации с помощью библиотеки проверки подлинности Microsoft для .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 749b81062fe542ae743e27483f6e9bd2323dafd9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921799"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Создание экземпляра конфиденциального клиентского приложения с параметрами конфигурации с помощью MSAL.NET

В этой статье описывается создание экземпляра [конфиденциального клиентского приложения](msal-client-applications.md) с помощью библиотеки проверки подлинности Майкрософт для .net (MSAL.NET).  Экземпляр приложения создается с параметрами конфигурации, определенными в файле параметров.

Перед инициализацией приложения необходимо сначала [зарегистрировать](quickstart-register-app.md) его, чтобы приложение можно было интегрировать с платформой Microsoft Identity. После регистрации может потребоваться следующая информация (которую можно найти в портал Azure):

- Идентификатор клиента (строка, представляющая GUID)
- URL-адрес поставщика удостоверений (с именем экземпляра) и аудитория входа для приложения. Эти два параметра вместе называются полномочиями.
- Идентификатор клиента, если вы пишете бизнес-приложение исключительно для вашей организации (также называется одноклиентским приложением).
- Секрет приложения (строка секрета клиента) или сертификат (типа X509Certificate2), если это конфиденциальное клиентское приложение.
- Для веб-приложений и иногда для общедоступных клиентских приложений (в частности, если приложению требуется использовать брокер) вы также настроили redirectUri, где поставщик удостоверений свяжется с приложением с маркерами безопасности.

## <a name="configure-the-application-from-the-config-file"></a>Настройка приложения из файла конфигурации
Имена свойств параметров в MSAL.NET соответствуют именам свойств `AzureADOptions` в ASP.NET Core, поэтому не нужно писать какой бы то ни было никакого связывающего кода.

Конфигурация приложения ASP.NET Core описана в файле *appSettings. JSON* :

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

Начиная с версии MSAL.NET v3. x, можно настроить конфиденциальное клиентское приложение в файле конфигурации.

В классе, где требуется настроить и создать экземпляр приложения, необходимо объявить объект `ConfidentialClientApplicationOptions`.  Свяжите конфигурацию, считанную из источника (включая файл appconfig. JSON), с экземпляром параметров приложения, используя метод `IConfigurationRoot.Bind()` из [пакета NuGet Microsoft. Extensions. Configuration. BINDER](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder):

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Это позволяет привязать содержимое раздела "AzureAD" файла *appSettings. JSON* к соответствующим свойствам объекта `ConfidentialClientApplicationOptions`.  Затем создайте объект `ConfidentialClientApplication`:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Добавить конфигурацию среды выполнения
В конфиденциальном клиентском приложении обычно имеется кэш для каждого пользователя. Поэтому необходимо получить кэш, связанный с пользователем, и сообщить построителю приложений, что вы хотите его использовать. Аналогичным образом, может иметься динамически вычисленный URI перенаправления. В этом случае код является следующим:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

