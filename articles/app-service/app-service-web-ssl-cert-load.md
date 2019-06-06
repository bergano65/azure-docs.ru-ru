---
title: Использование SSL-сертификата клиента в коде приложения Службы приложений Azure | Документация Майкрософт
description: Узнайте, как подключаться к удаленным ресурсам с помощью необходимых им сертификатов клиента.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ead1892062912840c9931ae60d11c90975ad26ac
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475101"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Использование SSL-сертификата в коде приложения службы приложений Azure

В этом практическом руководстве показано, как использовать общедоступный или частный сертификаты в коде приложения. Это будет удобно, если приложение обращается к внешней службе, для которой требуется аутентификация на основе сертификата.

Подход с использованием сертификатов в коде используются SSL в службе приложений, который требует приложения, которые будут в **основные** уровня или более поздней версии. Кроме того, вы можете [включать файл сертификата в репозиторий приложения](#load-certificate-from-file), но это не рекомендуется для закрытых сертификатов.

Доверив управление SSL-сертификатами службе приложений, вы сможете разделить сертификаты и код приложения, защитив таким образом конфиденциальные данные.

## <a name="upload-a-private-certificate"></a>Передача закрытого сертификата

Перед отправкой закрытый сертификат, убедитесь, что [он удовлетворяет всем требованиям](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), за исключением того, что это необязательно должен быть настроен для проверки подлинности сервера.

Когда вы будете готовы к отправке, выполните следующую команду <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Скопируйте отпечаток сертификата и см. в разделе [сделать сертификат доступным](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Передача общего сертификата

Открытые сертификаты поддерживаются в *.cer* формат. Чтобы передать открытый сертификат, <a href="https://portal.azure.com" target="_blank">портала Azure</a>и перейдите к своему приложению.

Нажмите кнопку **параметры SSL** > **открытых сертификатов (.cer)**  > **передать открытый сертификат** в левой области навигации приложения.

В **имя**, введите имя для сертификата. В **CER-файл сертификата**, выберите CER-файл.

Щелкните **Отправить**.

![Передача открытого сертификата](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

После отправки сертификата, скопируйте отпечаток сертификата и см. в разделе [сделать сертификат доступным](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Импортировать сертификат службы приложений

См. в разделе [приобретение и Настройка SSL-сертификат для службы приложений Azure](web-sites-purchase-ssl-web-site.md).

Когда сертификат будет импортирован, скопируйте отпечаток сертификата и см. в разделе [сделать сертификат доступным](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>Сделать сертификат доступным

Чтобы использовать сертификат, загруженный или импортированный в коде приложения, сделать его отпечаток доступным с `WEBSITE_LOAD_CERTIFICATES` параметр приложения, выполнив следующую команду в <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Чтобы сделать доступными все сертификаты, присвоено значение `*`.

> [!NOTE]
> Этот параметр накладывает указанных сертификатов в [текущей User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) хранилище для большинства ценовые категории, но в **Isolated** уровня (т. е. приложение запускается в [среды службы приложений](environment/intro.md)), она помещает сертификаты в [локального Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) хранения.
>

![Настройка параметров приложения](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

По завершении щелкните **Сохранить**.

Настроенные сертификаты теперь готовы для использования в коде.

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
## <a name="load-certificate-from-file"></a>Загрузка сертификата из файла

Если вам нужно загрузить файл сертификата из каталога приложения, лучше передать его при помощи [FTPS](deploy-ftp.md) вместо [Git](deploy-local-git.md), например. Следует хранить конфиденциальные данные, например закрытый сертификат из системы управления версиями.

Несмотря на то, что вы загружаете файл непосредственно в коде .NET, библиотека по-прежнему проверяет, загружен ли текущий профиль пользователя. Для загрузки текущего профиля пользователя, укажите `WEBSITE_LOAD_USER_PROFILE` параметр приложения с помощью следующей команды в <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

После задания этого параметра следующие C# пример загружает сертификат с именем `mycert.pfx` из `certs` каталог репозитория приложения.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
