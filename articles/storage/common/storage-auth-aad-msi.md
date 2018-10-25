---
title: Проверка подлинности доступа к большим двоичным объектам и очередям с помощью управляемых удостоверений Azure Active Directory для ресурсов Azure (предварительная версия) (служба хранилища Azure) | Документация Майкрософт
description: Хранилище BLOB-объектов и хранилище очередей поддерживает проверку подлинности Azure Active Directory с помощью управляемых удостоверений для ресурсов Azure. Вы можете использовать управляемые удостоверения для ресурсов Azure, чтобы проверить подлинность доступа к BLOB-объектам и очередям из приложений, запущенных на виртуальных машинах Azure, приложений-функций, масштабируемых наборов виртуальных машин и т д. Используя управляемые экземпляры для ресурсов Azure и мощные возможности проверки подлинности Azure AD, можно избежать хранения учетных данных в приложениях, выполняемых в облаке.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/05/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: c06dc83f8b460e6215448fbc9e4ac42c749b5922
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49354008"
---
# <a name="authenticate-access-to-blobs-and-queues-with-managed-identities-for-azure-resources-preview"></a>Аутентификация доступа к большим двоичным объектам и очередям с помощью управляемых удостоверений для ресурсов Azure (предварительная версия)

Хранилище BLOB-объектов и очередей поддерживают проверку подлинности Azure Active Directory (Azure AD) с использованием [управляемых удостоверений для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md). Вы можете использовать управляемые удостоверения для ресурсов Azure, чтобы проверить подлинность доступа к BLOB-объектам и очередям из приложений, запущенных на виртуальных машинах Azure, приложений-функций, масштабируемых наборов виртуальных машин и т д. Используя управляемые экземпляры для ресурсов Azure и мощные возможности проверки подлинности Azure AD, можно избежать хранения учетных данных в приложениях, выполняемых в облаке.  

Чтобы предоставить разрешения управляемому удостоверению на доступ к контейнеру больших двоичных объектов или очереди, управляемому удостоверению, которое включает в себя разрешения для этого ресурса в соответствующей области, необходимо назначить контроль доступа на основе ролей. Дополнительные сведения о ролях RBAC в службе хранилища см. в разделе [Manage access rights to Azure Storage data with RBAC (Preview)](storage-auth-aad-rbac.md) (Управление правами доступа к данным в службе хранилища Azure с помощью RBAC (предварительная версия)). 

В этой статье описана проверка подлинности в хранилище BLOB-объектов или очередей Azure с помощью управляемого удостоверения с виртуальной машины Azure.  

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="enable-managed-identities-on-a-vm"></a>Включение управляемых удостоверений на виртуальной машине

Перед тем, как вы сможете использовать управляемые удостоверения для ресурсов Azure, чтобы проверить подлинность доступа к большим двоичным объектам или очередям с виртуальной машины, нужно включить эти управляемые удостоверения для ресурсов Azure на виртуальной машине. Сведения о включении управляемых удостоверений для ресурсов Azure см. в одной из следующих статей.

- [портал Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Интерфейс командной строки Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Шаблон Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Пакеты SDK для Azure](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="get-a-managed-identity-access-token"></a>Получение маркера доступа управляемого удостоверения

Чтобы проверить подлинность с помощью управляемого удостоверения, приложение или скрипт должны получить маркер доступа управляемого удостоверения. Дополнительные сведения о том, как получить маркер доступа, см. в статье [Как использовать управляемые удостоверения для ресурсов Azure на виртуальной машине Azure для получения маркера доступа](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Пример кода .NET: создание блочного BLOB-объекта

В этом примере кода предполагается наличие маркера доступа управляемого удостоверения. Маркер доступа используется для авторизации управляемого удостоверения для создания блочного BLOB-объекта.

### <a name="add-references-and-using-statements"></a>Добавление ссылок и инструкций using  

В Visual Studio установите предварительную версию клиентской библиотеки службы хранилища Azure. В меню **Сервис** выберите **Диспетчер пакетов NuGet**, а затем — **Консоль диспетчера пакетов**. Введите приведенную ниже команду в консоли.

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

Добавьте приведенные ниже инструкции using в код.

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>Создание учетных данных на основе маркера доступа управляемого удостоверения

Чтобы создать блочный BLOB-объект, используйте класс **TokenCredentials**, предоставляемый предварительной версией пакета. Создайте экземпляр **TokenCredentials**, передав полученный ранее маркер доступа управляемого удостоверения.

```dotnet
// Create storage credentials from your managed identity access token.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Интеграция Azure AD со службой хранилища Azure требует использования протокола HTTPS для операций службы хранилища Azure.

## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше о ролях RBAC для службы хранилища Azure, ознакомившись с разделом [Manage access rights to Azure Storage data with RBAC (Preview)](storage-auth-aad-rbac.md) (Управление правами доступа к данным в службе хранилища Azure с помощью RBAC (предварительная версия)).
- Чтобы узнать об авторизации доступа к контейнерам и очередям из приложений службы хранилища, ознакомьтесь с разделом [Authenticate with Azure Active Directory from an Azure Storage application (Preview)](storage-auth-aad-app.md) (Аутентификация с помощью Azure Active Directory из приложения службы хранилища Azure (предварительная версия)).
- Чтобы узнать о входе в Azure CLI и PowerShell с использованием идентификатора Azure AD, ознакомьтесь с разделом [Использование удостоверения Azure AD для доступа к службе хранилища Azure с помощью интерфейса командной строки или PowerShell (предварительная версия)](storage-auth-aad-script.md).
- Дополнительные сведения об интеграции Azure AD для очередей и больших двоичных объектов Azure доступны в записи блога команды разработчиков службы хранилища Azure [Announcing the Preview of Azure AD Authentication for Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/) (Объявление о выпуске предварительной версии аутентификации Azure AD для службы хранилища Azure).
