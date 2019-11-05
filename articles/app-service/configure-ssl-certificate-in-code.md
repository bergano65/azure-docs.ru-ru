---
title: Использование SSL-сертификата в коде приложения — служба приложений Azure | Документация Майкрософт
description: Узнайте, как подключаться к удаленным ресурсам с помощью необходимых им сертификатов клиента.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/16/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1f042f72f82d2198472fe81670c697c0c4b28321
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513696"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Использование SSL-сертификата в коде приложения службы приложений Azure

Код приложения службы приложений может действовать как клиент и обращаться к внешней службе, для которой требуется проверка подлинности на сертификат. В этом пошаговом руководство показано, как использовать общедоступные или частные сертификаты в коде приложения.

Этот подход к использованию сертификатов в коде использует функциональность SSL в службе приложений, которая требует, чтобы ваше приложение было на уровне **Basic** или выше. Кроме того, можно [включить файл сертификата в репозиторий приложения](#load-certificate-from-file), но не рекомендуется использовать его для частных сертификатов.

Доверив управление SSL-сертификатами службе приложений, вы сможете разделить сертификаты и код приложения, защитив таким образом конфиденциальные данные.

## <a name="prerequisites"></a>Технические условия

Ознакомьтесь со следующими статьями:

- [Создано приложение службы приложений](/azure/app-service/).
- [Добавление сертификата в приложение](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Найти отпечаток

В <a href="https://portal.azure.com" target="_blank">портал Azure</a>в меню слева выберите **службы приложений** >  **\<имя приложения >** .

В левой области навигации приложения выберите **Параметры TLS/SSL**, а затем выберите **Сертификаты закрытого ключа (PFX)** или **Сертификаты открытого ключа (CER)** .

Найдите сертификат, который вы хотите использовать, и скопируйте отпечаток.

![Копирование отпечатка сертификата](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="load-the-certificate"></a>Загрузка сертификата

Чтобы использовать сертификат в коде приложения, добавьте его отпечаток в параметр приложения `WEBSITE_LOAD_CERTIFICATES`, выполнив следующую команду в <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Чтобы сделать все сертификаты доступными, установите значение `*`.

> [!NOTE]
> Этот параметр помещает указанные сертификаты в [Текущее хранилище усер\ми](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) для большинства ценовых категорий, но на **изолированном** уровне (т. е. приложение выполняется в [Среда службы приложений](environment/intro.md)) помещает сертификаты в [локальный мачине\ми](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) сообщений.
>

Настроенные сертификаты теперь готовы к использованию в коде.

## <a name="load-the-certificate-in-code"></a>Загрузка сертификата в коде

Когда сертификат будет доступен, к нему можно обращаться в коде C# через отпечаток сертификата. Следующий код загружает сертификат с отпечатком `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

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

<a name="file"></a>
## <a name="load-certificate-from-file"></a>Загрузить сертификат из файла

Если необходимо загрузить файл сертификата из каталога приложения, лучше передать его с помощью [FTPS](deploy-ftp.md) , а не [Git](deploy-local-git.md), например. Конфиденциальные данные следует учитывать как закрытый сертификат из системы управления версиями.

Несмотря на то, что файл загружается непосредственно в код .NET, Библиотека по-прежнему проверяет, загружен ли текущий профиль пользователя. Чтобы загрузить профиль текущего пользователя, задайте параметр приложения `WEBSITE_LOAD_USER_PROFILE` с помощью следующей команды в <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

После установки этого параметра в следующем C# примере загружается сертификат с именем `mycert.pfx` из каталога `certs` репозитория приложения.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

## <a name="more-resources"></a>Дополнительные ресурсы

* [Защита настраиваемого DNS-имени с помощью привязки SSL](configure-ssl-bindings.md)
* [Принудительное использование HTTPS](configure-ssl-bindings.md#enforce-https)
* [Принудительное применение TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [FAQ : App Service Certificates](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/) (Вопросы по сертификатам службы приложений и ответы на них)
