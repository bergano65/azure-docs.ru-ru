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
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6820daf34e63fd48e83c645e7509a3256bc8435b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066989"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Использование SSL-сертификата в коде приложения службы приложений Azure

В этом пошаговом руководство показано, как использовать общедоступные или частные сертификаты в коде приложения. Это будет удобно, если приложение обращается к внешней службе, для которой требуется аутентификация на основе сертификата.

Этот подход к использованию сертификатов в коде использует функциональность SSL в службе приложений, которая требует, чтобы ваше приложение было на уровне **Basic** или выше. Кроме того, можно [включить файл сертификата в репозиторий приложения](#load-certificate-from-file), но не рекомендуется использовать его для частных сертификатов.

Доверив управление SSL-сертификатами службе приложений, вы сможете разделить сертификаты и код приложения, защитив таким образом конфиденциальные данные.

## <a name="upload-a-private-certificate"></a>Передача закрытого сертификата

Перед отправкой частного сертификата убедитесь, что [он удовлетворяет всем требованиям](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), за исключением того, что его не нужно настраивать для проверки подлинности сервера.

Когда вы будете готовы к отправке, выполните следующую команду в <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Скопируйте отпечаток сертификата и см. раздел [обеспечение доступности сертификата](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Передача общего сертификата

Общедоступные сертификаты поддерживаются в формате *CER* . Чтобы отправить открытый сертификат, <a href="https://portal.azure.com" target="_blank">портал Azure</a>и перейдите к своему приложению.

Щелкните **Параметры** > SSL**открытые сертификаты (CER)**  > **отправить открытый сертификат** в левой области навигации приложения.

В поле **имя**введите имя сертификата. В **файле сертификата CER**выберите файл CER.

Щелкните **Отправить**.

![Передача открытого сертификата](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

После отправки сертификата скопируйте отпечаток сертификата и [Убедитесь, что сертификат доступен](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Импорт сертификата службы приложений

См. статью [приобретение и Настройка SSL-сертификата для службы приложений Azure](web-sites-purchase-ssl-web-site.md).

После импорта сертификата скопируйте отпечаток сертификата и [Убедитесь, что сертификат доступен](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>Предоставление доступа к сертификату

Чтобы использовать отправленный или импортированный сертификат в коде приложения, сделайте его отпечатком доступным с `WEBSITE_LOAD_CERTIFICATES` помощью параметра приложения, выполнив следующую команду в <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Чтобы сделать все сертификаты доступными, задайте значение `*`.

> [!NOTE]
> Этот параметр помещает указанные сертификаты в [Текущее хранилище усер\ми](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) для большинства ценовых категорий, но на изолированном уровне (т. е. приложение выполняется в [Среда службы приложений](environment/intro.md)) помещает сертификаты в [локальный мачине\ми](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) сообщений.
>

![Настройка параметров приложения](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

По завершении щелкните **Сохранить**.

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

Несмотря на то, что файл загружается непосредственно в код .NET, Библиотека по-прежнему проверяет, загружен ли текущий профиль пользователя. Чтобы загрузить текущий профиль пользователя, задайте `WEBSITE_LOAD_USER_PROFILE` параметр приложения с помощью следующей команды в <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

После установки этого параметра в следующем C# примере загружается сертификат, вызываемый `certs` `mycert.pfx` из каталога репозитория приложения.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
