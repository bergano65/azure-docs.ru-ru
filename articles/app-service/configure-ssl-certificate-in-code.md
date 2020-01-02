---
title: Использование SSL-сертификата в коде
description: Узнайте, как использовать сертификаты клиента в коде. Проверяйте подлинность с помощью удаленных ресурсов с помощью сертификата клиента или выполните с ними задачи шифрования.
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d783b61c372c7d0f8cca13106bf297ab9b55c424
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671894"
---
# <a name="use-an-ssl-certificate-in-your-code-in-azure-app-service"></a>Использование SSL-сертификата в коде в службе приложений Azure

В коде приложения можно получить доступ к [общедоступным или частным сертификатам, добавляемым в службу приложений](configure-ssl-certificate.md). Код приложения может действовать как клиент и обращаться к внешней службе, для которой требуется проверка подлинности сертификата, или же может потребоваться выполнить криптографические задачи. В этом пошаговом руководство показано, как использовать общедоступные или частные сертификаты в коде приложения.

Этот подход к использованию сертификатов в коде использует функциональность SSL в службе приложений, которая требует, чтобы ваше приложение было на уровне **Basic** или выше. Если приложение находится на уровне **Free** или **Shared** , можно [включить файл сертификата в репозиторий приложения](#load-certificate-from-file).

Доверив управление SSL-сертификатами службе приложений, вы сможете разделить сертификаты и код приложения, защитив таким образом конфиденциальные данные.

## <a name="prerequisites"></a>Технические условия

Ознакомьтесь со следующими статьями:

- [Создано приложение службы приложений](/azure/app-service/).
- [Добавление сертификата в приложение](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Найти отпечаток

На <a href="https://portal.azure.com" target="_blank">портале Azure</a> в меню слева выберите **Службы приложений** >  **\<имя_приложения>** .

В левой области навигации приложения выберите **Параметры TLS/SSL**, а затем выберите **Сертификаты закрытого ключа (PFX)** или **Сертификаты открытого ключа (CER)** .

Найдите сертификат, который вы хотите использовать, и скопируйте отпечаток.

![Копирование отпечатка сертификата](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Предоставление доступа к сертификату

Чтобы получить доступ к сертификату в коде приложения, добавьте его отпечаток в параметр приложения `WEBSITE_LOAD_CERTIFICATES`, выполнив следующую команду в <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Чтобы сделать все сертификаты доступными, установите значение `*`.

## <a name="load-certificate-in-windows-apps"></a>Загрузка сертификата в приложениях Windows

Параметр приложения `WEBSITE_LOAD_CERTIFICATES` делает указанные сертификаты доступными для приложения Windows, размещенного в хранилище сертификатов Windows, а расположение зависит от [ценовой](overview-hosting-plans.md)категории:

- **Изолированный** уровень в [локальной мачине\ми](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- Все остальные уровни — в [текущем усер\ми](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

В C# коде вы получите доступ к сертификату по отпечатку сертификата. Следующий код загружает сертификат с отпечатком `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

В коде Java вы получите доступ к сертификату из хранилища Windows-MY, используя поле общего имени субъекта (см. раздел [сертификат открытого ключа](https://en.wikipedia.org/wiki/Public_key_certificate)). В следующем коде показано, как загрузить сертификат закрытого ключа:

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

Дополнительные сведения о языках, которые не поддерживают или предлагают недостаточную поддержку хранилища сертификатов Windows, см. [в разделе Загрузка сертификата из файла](#load-certificate-from-file).

## <a name="load-certificate-in-linux-apps"></a>Загрузка сертификата в приложениях Linux

`WEBSITE_LOAD_CERTIFICATES` параметры приложения предоставляет доступ к указанным сертификатам для размещенных в Linux приложений (включая пользовательские приложения-контейнеры) в виде файлов. Файлы находятся в следующих каталогах:

- Частные сертификаты — `/var/ssl/private` (`.p12` файлы)
- Общедоступные сертификаты — `/var/ssl/certs` (файлы `.der`)

Имена файлов сертификатов — это отпечатки сертификата. В следующем C# примере кода показано, как загрузить открытый сертификат в приложение Linux.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Чтобы узнать, как загрузить SSL-сертификат из файла в Node. js, PHP, Python, Java или Ruby, см. документацию по соответствующему языку или Web Platform.

## <a name="load-certificate-from-file"></a>Загрузить сертификат из файла

Если вам нужно загрузить файл сертификата, который вы перегрузили вручную, лучше передать сертификат с помощью [FTPS](deploy-ftp.md) вместо [Git](deploy-local-git.md), например. Конфиденциальные данные следует учитывать как закрытый сертификат из системы управления версиями.

> [!NOTE]
> ASP.NET и ASP.NET Core в Windows должны получить доступ к хранилищу сертификатов, даже если вы загрузили сертификат из файла. Чтобы загрузить файл сертификата в приложение Windows .NET, загрузите профиль текущего пользователя с помощью следующей команды в <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Этот подход к использованию сертификатов в коде использует функциональность SSL в службе приложений, которая требует, чтобы ваше приложение было на уровне **Basic** или выше.

В следующем C# примере общедоступный сертификат загружается из относительного пути в приложении:

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Чтобы узнать, как загрузить SSL-сертификат из файла в Node. js, PHP, Python, Java или Ruby, см. документацию по соответствующему языку или Web Platform.

## <a name="more-resources"></a>Дополнительные ресурсы

* [Защита настраиваемого DNS-имени с помощью привязки SSL](configure-ssl-bindings.md)
* [Принудительное использование HTTPS](configure-ssl-bindings.md#enforce-https)
* [Принудительное применение TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [FAQ : App Service Certificates](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/) (Вопросы по сертификатам службы приложений и ответы на них)
