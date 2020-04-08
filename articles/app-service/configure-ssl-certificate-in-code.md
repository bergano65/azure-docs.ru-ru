---
title: Использование сертификата TLS/SSL в коде
description: Узнайте, как использовать сертификаты клиента в коде. Authenticate с удаленными ресурсами с сертификатом клиента, или запустить криптографические задачи с ними.
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d76bac60bae11f0843d81de523030154af62a373
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811696"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Используйте сертификат TLS/SSL в коде в службе приложений Azure

В коде приложения вы можете получить доступ к [общедоступным или частным сертификатам, которые вы добавляете в Службу app Service.](configure-ssl-certificate.md) Код приложения может выступать в качестве клиента и получить доступ к внешней службе, которая требует проверки подлинности сертификата, или, возможно, потребуется выполнять криптографические задачи. В этом руководстве показано, как использовать государственные или частные сертификаты в коде приложения.

Такой подход к использованию сертификатов в коде использует функциональность TLS в службе приложений, которая требует, чтобы ваше приложение было в **базовом** уровне или выше. Если ваше приложение находится в **свободном** или **общем** уровне, вы можете [включить файл сертификата в репозиторий приложения.](#load-certificate-from-file)

Когда вы позволяете Службе App управлять сертификатами TLS/SSL, вы можете отдельно сохранять сертификаты и код приложения и защищать конфиденциальные данные.

## <a name="prerequisites"></a>Предварительные требования

Ознакомьтесь со следующими статьями:

- [Создание приложения службы приложений](/azure/app-service/)
- [Добавление сертификата в приложение](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Найти отпечаток пальца

На <a href="https://portal.azure.com" target="_blank">портале Azure</a>из левого меню выберите приложение **App Services** > **\<>. **

Из левой навигации вашего приложения выберите **настройки TLS/SSL,** затем выберите **Сертификаты частных ключей (.pfx)** или **Сертификаты открытых ключей (.cer).**

Найдите сертификат, который вы хотите использовать, и скопируйте отпечаток пальца.

![Копирование отпечатка пальца сертификата](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Сделать сертификат доступным

Чтобы получить доступ к сертификату в коде `WEBSITE_LOAD_CERTIFICATES` приложения, добавьте его отпечаток пальца в настройку приложения, запустив следующую команду в <a target="_blank" href="https://shell.azure.com" >облачной оболочке:</a>

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Чтобы сделать все сертификаты доступными, установите `*`значение.

## <a name="load-certificate-in-windows-apps"></a>Сертификат загрузки в приложениях Windows

Настройка `WEBSITE_LOAD_CERTIFICATES` приложения делает указанные сертификаты доступными для вашего приложения, размещенного в Windows, в магазине сертификатов Windows, и местоположение зависит от [уровня ценообразования:](overview-hosting-plans.md)

- **Изолированный** ярус - в [местной машине .](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) 
- Все остальные уровни - в [текущем пользовательском .](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)

В коде C', вы получаете доступ к сертификату с помощью отпечатка пальца сертификата. Следующий код загружает сертификат с отпечатком `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

В Java-коде вы получаете доступ к сертификату из магазина "Windows-MY" с помощью поля "Общее имя субъекта" [(см. Открытый сертификат клавиши).](https://en.wikipedia.org/wiki/Public_key_certificate) Следующий код показывает, как загрузить частный сертификат ключа:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

Для языков, которые не поддерживают или не обеспечивают недостаточную поддержку для магазина сертификатов Windows, [см.](#load-certificate-from-file)

## <a name="load-certificate-in-linux-apps"></a>Сертификат загрузки в приложениях Linux

Настройки `WEBSITE_LOAD_CERTIFICATES` приложения делают указанные сертификаты доступными для размещенных приложений Linux (включая пользовательские контейнерные приложения) в качестве файлов. Файлы находятся в следующих каталогах:

- Частные сертификаты - `/var/ssl/private` (файлы) `.p12`
- Публичные сертификаты - `/var/ssl/certs` (файлы) `.der`

Имена файлов сертификата являются отпечатками отпечатков пальцев сертификата. Следующий код C's показывает, как загрузить общедоступный сертификат в приложении Linux.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Чтобы узнать, как загрузить сертификат TLS/SSL из файла в Node.js, PHP, Python, Java или Ruby, см.

## <a name="load-certificate-from-file"></a>Сертификат загрузки из файла

Если вам нужно загрузить файл сертификата, который вы загружаете вручную, лучше загрузить сертификат, например, с помощью [FTPS](deploy-ftp.md) вместо [Git.](deploy-local-git.md) Необходимо не допустить, чтобы конфиденциальные данные, такие как частный сертификат, не контролировались исходным управлением.

> [!NOTE]
> ASP.NET и ASP.NET Core на Windows должны получить доступ к магазину сертификатов, даже если вы загружаете сертификат из файла. Чтобы загрузить файл сертификата в приложение Windows .NET, загрузите текущий профиль пользователя со следующей командой в <a target="_blank" href="https://shell.azure.com" >облачной оболочке:</a>
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Такой подход к использованию сертификатов в коде использует функциональность TLS в службе приложений, которая требует, чтобы ваше приложение было в **базовом** уровне или выше.

Следующий пример C's загружает общедоступный сертификат с относительного пути в приложении:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Чтобы узнать, как загрузить сертификат TLS/SSL из файла в Node.js, PHP, Python, Java или Ruby, см.

## <a name="more-resources"></a>Дополнительные ресурсы

* [Защищайте пользовательское имя DNS с привязкой TLS/SSL в службе приложений Azure](configure-ssl-bindings.md)
* [Принудительное использование HTTPS](configure-ssl-bindings.md#enforce-https)
* [Принудительное применение TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [FAQ : App Service Certificates](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/) (Вопросы по сертификатам службы приложений и ответы на них)
