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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32fbd4af78e02dad2a8a74ee21f9cb8c6ef0a976
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175496"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Управляющее приложение, вызывающее веб-API — конфигурация кода

Узнайте, как настроить код для управляющего приложения, вызывающего веб-API.

## <a name="msal-libraries-supporting-daemon-apps"></a>Библиотеки MSAL, поддерживающие приложения управляющей программы

Библиотеки Майкрософт, поддерживающие управляющие приложения:

  Библиотека MSAL | Описание
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Для создания управляющего приложения поддерживаются платформы .NET Framework и платформы .NET Core (не UWP, Xamarin. iOS и Xamarin. Android, так как эти платформы используются для создания общедоступных клиентских приложений).
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Выполняется разработка — в общедоступной предварительной версии
  ![Java:](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Выполняется разработка — в общедоступной предварительной версии

## <a name="configuration-of-the-authority"></a>Настройка центра

Учитывая, что управляющие приложения не используют делегированные разрешения, но разрешения приложений, их *поддерживаемый тип учетной записи* не могут быть *учетными записями в любом каталоге Организации и личных учетных записях Майкрософт (например, Skype, Xbox, Outlook.com)* . В действительности нет прав администратора клиента для предоставления согласия на приложение управляющей программы для личных учетных записей Майкрософт. Вам потребуется выбрать *учетные записи в моей организации* или *учетных записях в любой организации*.

Поэтому центр, указанный в конфигурации приложения, должен быть клиентом-ED (указав идентификатор клиента или доменное имя, связанное с вашей организацией).

Если вы являетесь независимым поставщиком программного обеспечения и хотите предоставить многоклиентское средство, можно использовать `organizations`. Но помните, что вы также должны объяснить своим заказчикам, как предоставить согласие администратора. Дополнительные сведения см. [в статье запрос согласия для всего клиента](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) . Кроме того, сейчас существует ограничение в MSAL: `organizations` разрешено, только если учетные данные клиента являются секретом приложения (а не сертификатом).

## <a name="application-configuration-and-instantiation"></a>Настройка и создание приложений

В библиотеках MSAL учетные данные клиента (секрет или сертификат) передаются в качестве параметра для создания конфиденциального клиентского приложения.

> [!IMPORTANT]
> Даже если приложение является консольным приложением, выполняемым в качестве службы, если это управляющее приложение, оно должно быть конфиденциальным клиентским приложением.

### <a name="configuration-file"></a>Файл конфигурации

Файл конфигурации определяет:

- центр или облачный экземпляр и tenantId
- Идентификатор ClientID, полученный при регистрации приложения
- Секрет клиента или сертификат

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[appSettings. JSON](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) из примера [консольной управляющей программы .NET Core](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) .

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Вы предоставляете clientSecret или certificateName. Оба параметра являются эксклюзивными.

# <a name="pythontabpython"></a>[Python](#tab/python)

При создании конфиденциального клиента с секретами клиента файл [Parameters. JSON](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) в образце [управляющей программы Python](https://github.com/Azure-Samples/ms-identity-python-daemon) выглядит следующим образом.

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

При создании конфиденциального клиента с сертификатами файл config [. JSON](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) в образце [управляющей программы Python](https://github.com/Azure-Samples/ms-identity-python-daemon) выглядит следующим образом.

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Ниже приведен класс, используемый в примерах разработки Java MSAL для настройки примеров: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
public class TestData {

    final static String TENANT_SPECIFIC_AUTHORITY = "https://login.microsoftonline.com/<TenantId>/";
    final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
    final static String CONFIDENTIAL_CLIENT_ID = "";
    final static String CONFIDENTIAL_CLIENT_SECRET = "";
}
```

---

### <a name="instantiation-of-the-msal-application"></a>Создание экземпляра приложения MSAL

Чтобы создать экземпляр приложения MSAL, необходимо:

- Добавление, ссылка или импорт пакета MSAL (в зависимости от языка)
- Затем конструкция различается в зависимости от того, используются ли клиентские секреты или сертификаты (или как расширенный сценарий, подписанные утверждения).

#### <a name="reference-the-package"></a>Ссылка на пакет

Сослаться на пакет MSAL в коде приложения.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Добавьте пакет NuGet [Microsoft. идентитиклиент](https://www.nuget.org/packages/Microsoft.Identity.Client) в приложение.
В MSAL.NET конфиденциальное клиентское приложение представлено интерфейсом `IConfidentialClientApplication`.
Использование пространства имен MSAL.NET в исходном коде

```CSharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
import msal
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
```

---

#### <a name="instantiate-the-confidential-client-application-with-client-secrets"></a>Создание экземпляра конфиденциального клиентского приложения с помощью секретов клиента

Ниже приведен код для создания экземпляра конфиденциального клиентского приложения с секретом клиента:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(TestData.CONFIDENTIAL_CLIENT_SECRET))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-client-certificate"></a>Создание экземпляра конфиденциального клиентского приложения с помощью сертификата клиента

Ниже приведен код для создания приложения с сертификатом.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

В MSAL. Java существует два строителя, которые создают экземпляры конфиденциального клиентского приложения с сертификатами:

```Java

InputStream pkcs12Certificate = ... ; /* containing PCKS12 formatted certificate*/
string certificatePassword = ... ;    /* contains the password to access the certificate */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(pkcs12Certificate, certificatePassword))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

или

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(rsaPrivateKey, publicKeyCertificate))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="advanced-scenario---instantiate-the-confidential-client-application-with-client-assertions"></a>Расширенный сценарий. Создание экземпляра конфиденциального клиентского приложения с утверждениями клиента

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Вместо секрета клиента или сертификата конфиденциальное клиентское приложение также может доказать свое удостоверение с помощью утверждений клиента.

MSAL.NET имеет два метода для предоставления подписанных утверждений для конфиденциального клиентского приложения:

- `.WithClientAssertion()`
- `.WithClientClaims()`

При использовании `WithClientAssertion` необходимо предоставить подписанный JWT. Этот расширенный сценарий подробно описан в [утверждениях клиента](msal-net-client-assertions.md) .

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

При использовании `WithClientClaims` MSAL.NET вычислит собой подписанное утверждение, содержащее утверждения, ожидаемые Azure AD, а также дополнительные клиентские утверждения, которые требуется отправить.
Ниже приведен фрагмент кода для этого:

```CSharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

Дополнительные сведения см. в разделе [утверждения клиента](msal-net-client-assertions.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

В MSAL Python можно предоставить клиентские заявки с помощью утверждений, которые будут подписаны закрытым ключом этого `ConfidentialClientApplication`.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Дополнительные сведения см. в справочной документации по MSAL Python для [конфидентиалклиентаппликатион](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java находится в общедоступной предварительной версии. Подписанные утверждения пока не поддерживаются.

---

## <a name="next-steps"></a>Дальнейшие действия

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Приложение управляющей программы — получение маркеров для приложения](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Приложение управляющей программы — получение маркеров для приложения](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Приложение управляющей программы — получение маркеров для приложения](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
