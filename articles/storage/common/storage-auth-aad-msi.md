---
title: Проверки подлинности при доступе к большим двоичным объектам и очередям с удостоверениями Azure Active Directory, управляемую для ресурсов Azure — хранилища Azure | Документация Майкрософт
description: Хранилище BLOB-объектов и хранилище очередей поддерживает проверку подлинности Azure Active Directory с помощью управляемых удостоверений для ресурсов Azure. Вы можете использовать управляемые удостоверения для ресурсов Azure, чтобы проверить подлинность доступа к BLOB-объектам и очередям из приложений, запущенных на виртуальных машинах Azure, приложений-функций, масштабируемых наборов виртуальных машин и т д. Используя управляемые экземпляры для ресурсов Azure и мощные возможности проверки подлинности Azure AD, можно избежать хранения учетных данных в приложениях, выполняемых в облаке.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 76bf36f63051f02d6c37261799342f424609f9b2
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58442302"
---
# <a name="authenticate-access-to-blobs-and-queues-with-managed-identities-for-azure-resources"></a>Проверки подлинности при доступе к большим двоичным объектам и очередям управляемые удостоверения для ресурсов Azure

Хранилище BLOB-объектов и очередей поддерживают проверку подлинности Azure Active Directory (Azure AD) с использованием [управляемых удостоверений для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md). Управляемые удостоверения для ресурсов Azure позволяют проверить подлинность доступа к BLOB-объектам и очередям с использованием учетных данных Azure AD из приложений, запущенных на виртуальных машинах Azure, приложений-функций, масштабируемых наборов виртуальных машин и т. д. Используя управляемые экземпляры для ресурсов Azure и мощные возможности проверки подлинности Azure AD, можно избежать хранения учетных данных в приложениях, выполняемых в облаке.  

Чтобы предоставить разрешения управляемому удостоверению на доступ к контейнеру больших двоичных объектов или очереди, управляемому удостоверению, которое включает в себя разрешения для этого ресурса в соответствующей области, необходимо назначить контроль доступа на основе ролей. Дополнительные сведения о ролях RBAC в службе хранилища, см. в разделе [управление права доступа к данным хранилища с помощью RBAC](storage-auth-aad-rbac.md). 

В этой статье описана проверка подлинности в хранилище BLOB-объектов или очередей Azure с помощью управляемого удостоверения с виртуальной машины Azure.  

## <a name="enable-managed-identities-on-a-vm"></a>Включение управляемых удостоверений на виртуальной машине

Перед тем, как вы сможете использовать управляемые удостоверения для ресурсов Azure, чтобы проверить подлинность доступа к большим двоичным объектам или очередям с виртуальной машины, нужно включить эти управляемые удостоверения для ресурсов Azure на виртуальной машине. Сведения о включении управляемых удостоверений для ресурсов Azure см. в одной из следующих статей.

- [портал Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Интерфейс командной строки Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Шаблон Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Пакеты SDK для Azure](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="assign-an-rbac-role-to-an-azure-ad-managed-identity"></a>Назначение роли RBAC для управляемого удостоверения Azure AD

Чтобы обеспечить проверку подлинности управляемого удостоверения из приложения службы хранилища Azure, нужно сначала настроить параметры управления доступом на основе ролей (RBAC) для этого управляемого удостоверения. Служба хранилища Azure определяет роли RBAC, которые включают в себя разрешения для контейнеров и очередей. При назначении роли RBAC управляемому удостоверению ему предоставляется доступ к соответствующему ресурсу. Дополнительные сведения см. в разделе [управление права доступа к данным BLOB-объектов Azure и очереди с помощью RBAC](storage-auth-aad-rbac.md).

## <a name="get-a-managed-identity-access-token"></a>Получение маркера доступа управляемого удостоверения

Чтобы проверить подлинность с помощью управляемого удостоверения, приложение или скрипт должны получить маркер доступа управляемого удостоверения. Дополнительные сведения о том, как получить маркер доступа, см. в статье [Как использовать управляемые удостоверения для ресурсов Azure на виртуальной машине Azure для получения маркера доступа](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

Для авторизации операций с большими двоичными объектами и очередями с использованием токена OAuth необходимо использовать протокол HTTPS.

## <a name="net-code-example-create-a-block-blob"></a>Пример кода .NET. Создание блочного BLOB-объекта

В этом примере кода предполагается наличие маркера доступа управляемого удостоверения. Маркер доступа используется для авторизации управляемого удостоверения для создания блочного BLOB-объекта.

### <a name="add-references-and-using-statements"></a>Добавление ссылок и инструкций using  

В Visual Studio установите клиентскую библиотеку службы хранилища Azure. В меню **Сервис** выберите **Диспетчер пакетов NuGet**, а затем — **Консоль диспетчера пакетов**. Введите приведенную ниже команду в консоли.

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

Добавьте приведенные ниже инструкции using в код.

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>Создание учетных данных на основе маркера доступа управляемого удостоверения

Чтобы создать блочный BLOB-объект, используйте **TokenCredentials** класса. Создайте экземпляр **TokenCredentials**, передав полученный ранее маркер доступа управляемого удостоверения.

```dotnet
// Create storage credentials from your managed identity access token.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Интеграция Azure AD со службой хранилища Azure требует использования протокола HTTPS для операций службы хранилища Azure.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о ролях RBAC для службы хранилища Azure, см. в разделе [управление права доступа к данным хранилища с помощью RBAC](storage-auth-aad-rbac.md).
- Чтобы узнать об авторизации доступа к контейнерам и очередям из приложений службы хранилища, ознакомьтесь с разделом [Authenticate with Azure Active Directory from an Azure Storage application (Preview)](storage-auth-aad-app.md) (Аутентификация с помощью Azure Active Directory из приложения службы хранилища Azure (предварительная версия)).
- Чтобы узнать, как для входа Azure CLI и PowerShell с помощью удостоверения Azure AD, см. в разделе [использование удостоверения Azure AD для доступа к хранилищу Azure с помощью интерфейса командной строки или PowerShell](storage-auth-aad-script.md).
