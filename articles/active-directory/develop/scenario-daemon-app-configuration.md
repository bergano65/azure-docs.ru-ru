---
title: Управляющая программа приложение вызова веб-API (конфигурация приложений) - платформой Microsoft identity
description: Узнайте, как создать управляющую программу, вызовы веб-API-интерфейсы (конфигурация приложений)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: d8d377db827a6548c380128624c21f4ae7896aff
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075330"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Вызовы веб-интерфейсы API — код конфигурации приложений управляющей программы

Узнайте, как настроить код для приложения управляющей программы, что вызовы веб-API.

## <a name="msal-libraries-supporting-daemon-apps"></a>Библиотеки MSAL вспомогательных приложений управляющей программы

Поддерживать приложения, управляющая программа библиотек Microsoft являются:

  Библиотека MSAL | ОПИСАНИЕ
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Поддерживаемые платформы для построения приложения управляющей программы — платформы .NET Framework и .NET Core (не UWP, Xamarin.iOS и Xamarin.Android, как эти платформы используются для построения общедоступных клиентских приложений)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Разработка решений в ход выполнения — в общедоступной предварительной версии
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL. Java | Разработка решений в ход выполнения — в общедоступной предварительной версии

## <a name="configuration-of-the-authority"></a>Конфигурации центра

Учитывая, что управляющая программа приложения не использовать делегированные разрешения, но разрешения приложения, их *поддерживается тип учетной записи* не может быть *учетных записей в любой организации directory и Microsoft персональных учетных записей () Например, Скайп, Xbox, Outlook.com)*. Действительно нет нет администратора клиента дать согласие приложений управляющей программы для личных учетных записей Майкрософт. Вам потребуется выбрать *учетные записи в моей организации* или *учетных записей в любой организации*.

Поэтому центра, указанное в конфигурации приложения должно быть клиента ed (с указанием идентификатора клиента или имя домена, связанный с вашей организацией). Если вы являетесь, независимых поставщиков программного обеспечения и хотите предоставляет средство несколькими клиентами, можно использовать `organizations`. Но имейте в виду, что также необходимо объяснить заказчикам описывается процесс предоставления согласия администратора. См. в разделе [запрос на получение согласия для клиента](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) сведения

## <a name="application-configuration-and-instantiation"></a>Настройка приложения и создание экземпляров

В библиотеках MSAL учетные данные клиента (секрет или сертификат), передаются как параметр конструкторе конфиденциального клиента приложения.

> [!IMPORTANT]
> Даже если приложение является консольным приложением, выполняющиеся как службы, если это приложение управляющей программы необходимо иметь конфиденциального клиентского приложения.

### <a name="msalnet"></a>MSAL.NET

Добавить [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) пакет NuGet для вашего приложения.

Используйте пространство имен MSAL.NET

```CSharp
using Microsoft.Identity.Client;
```

Управляющее приложение веб-презентация `IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

Ниже приведен код, чтобы создать приложение с секрет приложения.

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

Ниже приведен код для создания приложения с помощью сертификата.

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

### <a name="msalpython"></a>MSAL.Python

```Python
# Create a preferably long-lived app instance which maintains a token cache.

app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache

    )
```

### <a name="msaljava"></a>MSAL. Java

```Java
PrivateKey key = getPrivateKey();
X509Certificate publicCertificate = getPublicCertificate();

// create clientCredential with public and private key
IClientCredential credential = ClientCredentialFactory.create(key, publicCertificate);

ConfidentialClientApplication cca = ConfidentialClientApplication
  .builder(CLIENT_ID, credential)
  .authority(AUTHORITY_MICROSOFT)
  .build();
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Управляющая программа приложение — получение маркеров для приложения](./scenario-daemon-acquire-token.md)