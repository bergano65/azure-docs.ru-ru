---
title: Приложение управляющей программы, вызывающее веб-API (Конфигурация приложения) — платформа Microsoft Identity
description: Узнайте, как создать управляющее приложение, вызывающее веб-API (Конфигурация приложения).
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 705545fd5167087be1a001c45f58907d6ff225e8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277823"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Управляющее приложение, вызывающее веб-API — конфигурация кода

Узнайте, как настроить код для управляющего приложения, вызывающего веб-API.

## <a name="msal-libraries-supporting-daemon-apps"></a>Библиотеки MSAL, поддерживающие приложения управляющей программы

Библиотеки Майкрософт, поддерживающие управляющие приложения:

  Библиотека MSAL | Описание
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Для создания управляющего приложения поддерживаются платформы .NET Framework и платформы .NET Core (не UWP, Xamarin. iOS и Xamarin. Android, так как эти платформы используются для создания общедоступных клиентских приложений).
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL. Языке | Выполняется разработка — в общедоступной предварительной версии
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL. Java | Выполняется разработка — в общедоступной предварительной версии

## <a name="configuration-of-the-authority"></a>Настройка центра

Учитывая, что управляющие приложения не используют делегированные разрешения, но разрешения приложений, их *поддерживаемый тип учетной записи* не могут быть *учетными записями в любом каталоге Организации и личных учетных записях Майкрософт (например, Skype, Xbox, Outlook.com)* . В действительности нет прав администратора клиента для предоставления согласия на приложение управляющей программы для личных учетных записей Майкрософт. Вам потребуется выбрать учетные *записи в моей организации* или *учетных записях в любой организации*.

Поэтому центр, указанный в конфигурации приложения, должен быть клиентом-ED (указав идентификатор клиента или доменное имя, связанное с вашей организацией).

Если вы являетесь независимым поставщиком программного обеспечения и хотите предоставить средство с несколькими клиентами, `organizations`вы можете использовать. Но помните, что вы также должны объяснить своим заказчикам, как предоставить согласие администратора. Дополнительные сведения см. [в статье запрос согласия для всего клиента](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) . Также существует ограничение в MSAL, которое `organizations` допустимо только в том случае, если учетные данные клиента являются секретом приложения (а не сертификатом). См. раздел [MSAL.NET bug #891](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/891)

## <a name="application-configuration-and-instantiation"></a>Настройка и создание приложений

В библиотеках MSAL учетные данные клиента (секрет или сертификат) передаются в качестве параметра для создания конфиденциального клиентского приложения.

> [!IMPORTANT]
> Даже если приложение является консольным приложением, выполняемым в качестве службы, если это управляющее приложение, оно должно быть конфиденциальным клиентским приложением.

### <a name="msalnet"></a>MSAL.NET

Добавьте пакет NuGet [Microsoft. идентитиклиент](https://www.nuget.org/packages/Microsoft.Identity.Client) в приложение.

Использовать пространство имен MSAL.NET

```CSharp
using Microsoft.Identity.Client;
```

Управляющее приложение будет представлено`IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

Ниже приведен код для создания приложения с секретом приложения.

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

Ниже приведен код для создания приложения с сертификатом.

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

Наконец, вместо секрета клиента или сертификата конфиденциальное клиентское приложение также может доказать свое удостоверение с помощью утверждений клиента. Этот расширенный сценарий подробно описан в [утверждениях клиента](msal-net-client-assertions.md) .


### <a name="msalpython"></a>MSAL. Языке

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

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Приложение управляющей программы — получение маркеров для приложения](./scenario-daemon-acquire-token.md)
