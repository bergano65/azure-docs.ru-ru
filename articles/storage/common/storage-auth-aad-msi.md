---
title: Авторизация доступа к BLOB-объектам и очередям с помощью Azure Active Directory и управляемых удостоверений для ресурсов Azure в службе хранилища Azure
description: Хранилище BLOB-объектов и очередей Azure поддерживает авторизацию доступа к ресурсам с помощью Azure Active Directory и управляемых удостоверений для ресурсов Azure. Управляемые удостоверения для ресурсов Azure можно использовать для авторизации доступа к BLOB-объектам и очередям из приложений, работающих на виртуальных машинах Azure, в приложениях-функциях, масштабируемых наборах виртуальных машин и других.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 66d867d33060aa931dbe42c534166e61ee7692fe
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534520"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Авторизация доступа к BLOB-объектам и очередям с Azure Active Directory и управляемыми удостоверениями для ресурсов Azure

Хранилище BLOB-объектов и очередей поддерживают проверку подлинности Azure Active Directory (Azure AD) с использованием [управляемых удостоверений для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md). Управляемые удостоверения для ресурсов Azure могут авторизовать доступ к данным большого двоичного объекта и очереди с помощью учетных данных Azure AD из приложений, работающих на виртуальных машинах Azure, приложений-функций, масштабируемых наборов виртуальных машин и других служб. Используя управляемые удостоверения для ресурсов Azure вместе с проверкой подлинности Azure AD, можно избежать хранения учетных данных в приложениях, выполняемых в облаке.  

В этой статье показано, как авторизовать доступ к данным большого двоичного объекта или очереди с виртуальной машины Azure с помощью управляемых удостоверений для ресурсов Azure. В нем также описывается тестирование кода в среде разработки.

## <a name="enable-managed-identities-on-a-vm"></a>Включение управляемых удостоверений на виртуальной машине

Прежде чем использовать управляемые удостоверения для ресурсов Azure для авторизации доступа к BLOB-объектам и очередям из виртуальной машины, сначала необходимо включить управляемые удостоверения для ресурсов Azure на виртуальной машине. Сведения о включении управляемых удостоверений для ресурсов Azure см. в одной из следующих статей.

- [Портал Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Шаблон Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Клиентские библиотеки Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Дополнительные сведения об управляемых удостоверениях см. в статье [управляемые удостоверения для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library"></a>Проверка подлинности с помощью библиотеки удостоверений Azure

Преимущество клиентской библиотеки Azure Identity заключается в том, что она позволяет использовать тот же код для проверки подлинности при выполнении приложения в среде разработки или в Azure. В коде, выполняющемся в среде Azure, клиентская библиотека проверяет подлинность управляемого удостоверения для ресурсов Azure. В среде разработки управляемое удостоверение не существует, поэтому клиентская библиотека проверяет подлинность пользователя или субъекта-службы в целях тестирования.

Клиентская библиотека удостоверений Azure для .NET выполняет проверку подлинности субъекта безопасности. Когда код выполняется в Azure, участник безопасности является управляемым удостоверением для ресурсов Azure.

После проверки подлинности клиентская библиотека удостоверений Azure получает учетные данные маркера. Затем эти учетные данные маркера инкапсулируются в объекте клиента службы, который вы создадите для выполнения операций с хранилищем Azure. Библиотека эффективно обрабатывает это, получая соответствующие учетные данные маркера.

Дополнительные сведения о клиентской библиотеке удостоверений Azure см. в статье [Клиентская библиотека удостоверений Azure для .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

### <a name="assign-role-based-access-control-rbac-roles-for-access-to-data"></a>Назначение ролей управления доступом на основе ролей (RBAC) для доступа к данным

Когда субъект безопасности Azure AD пытается получить доступ к данным большого двоичного объекта или очереди, этот субъект безопасности должен иметь разрешения для ресурса. Независимо от того, является ли участник безопасности управляемым удостоверением в Azure или учетной записью пользователя Azure AD, выполняющего код в среде разработки, участнику безопасности должна быть назначена роль RBAC, которая предоставляет доступ к данным большого двоичного объекта или очереди в службе хранилища Azure. Сведения о назначении разрешений через RBAC см. в разделе **назначение РОЛЕЙ RBAC для прав доступа** в статье [авторизация доступа к BLOB-объектам и очередям Azure с помощью Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

### <a name="authenticate-the-user-in-the-development-environment"></a>Проверка подлинности пользователя в среде разработки

Если код выполняется в среде разработки, проверка подлинности может выполняться автоматически, или в зависимости от используемых инструментов может потребоваться вход в браузер. Microsoft Visual Studio поддерживает единый вход (SSO), чтобы активная учетная запись пользователя Azure AD автоматически использовалась для проверки подлинности. Дополнительные сведения об единого входа см. [в статье единый вход в приложения](../../active-directory/manage-apps/what-is-single-sign-on.md).

Другие средства разработки могут предложить вам войти в систему через веб-браузер.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Проверка подлинности субъекта-службы в среде разработки

Если ваша среда разработки не поддерживает единый вход или вход через веб-браузер, то можно использовать субъект службы для проверки подлинности из среды разработки.

#### <a name="create-the-service-principal"></a>Создание субъекта-службы

Чтобы создать субъект-службу с Azure CLI и назначить роль RBAC, вызовите команду [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) . Укажите роль доступа к данным службы хранилища Azure для назначения новому субъекту-службе. Кроме того, укажите область для назначения роли. Дополнительные сведения о встроенных ролях, предоставляемых для службы хранилища Azure, см. [в статье встроенные роли для ресурсов Azure](../../role-based-access-control/built-in-roles.md).

Если у вас нет достаточных разрешений для назначения роли субъекту-службе, может потребоваться попросить владельца или администратора учетной записи выполнить назначение ролей.

В следующем примере используется Azure CLI для создания нового субъекта-службы и назначения ему роли **модуля чтения данных BLOB-объекта хранилища** с использованием области учетной записи.

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Команда `az ad sp create-for-rbac` возвращает список свойств субъекта-службы в формате JSON. Скопируйте эти значения, чтобы их можно было использовать для создания необходимых переменных среды на следующем шаге.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Назначений ролей RBAC может потребоваться несколько минут для распространения.

#### <a name="set-environment-variables"></a>Настройка переменных среды

Клиентская библиотека удостоверений Azure считывает значения из трех переменных среды во время выполнения для проверки подлинности субъекта-службы. В следующей таблице описывается значение, которое задается для каждой переменной среды.

|Переменная среды|Value
|-|-
|`AZURE_CLIENT_ID`|Идентификатор приложения для субъекта-службы
|`AZURE_TENANT_ID`|Идентификатор клиента Azure AD субъекта-службы
|`AZURE_CLIENT_SECRET`|Пароль, созданный для субъекта-службы

> [!IMPORTANT]
> После задания переменных среды закройте и снова откройте окно консоли. Если вы используете Visual Studio или другую среду разработки, может потребоваться перезапустить среду разработки, чтобы зарегистрировать новые переменные среды.

Дополнительные сведения см. [в статье Создание удостоверения для приложения Azure на портале](../../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="install-client-library-packages"></a>Установка пакетов клиентской библиотеки

В примерах, приведенных в этой статье, используется последняя версия клиентской библиотеки службы хранилища Azure для хранилища BLOB-объектов. Чтобы установить пакет, выполните следующую команду в консоли диспетчера пакетов NuGet:

```powershell
Install-Package Azure.Storage.Blobs
```

Примеры в этой статье также используют последнюю версию [клиентской библиотеки удостоверений Azure для .NET](https://www.nuget.org/packages/Azure.Identity/) для аутентификации с помощью учетных данных Azure AD. Чтобы установить пакет, выполните следующую команду в консоли диспетчера пакетов NuGet:

```powershell
Install-Package Azure.Identity
```

## <a name="net-code-example-create-a-block-blob"></a>Пример кода .NET: создание блочного BLOB-объекта

Добавьте в код следующие директивы `using` для использования Azure Identity и клиентских библиотек службы хранилища Azure.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

Чтобы получить учетные данные маркера, которые ваш код может использовать для авторизации запросов в службе хранилища Azure, создайте экземпляр класса [дефаултазурекредентиал](/dotnet/api/azure.identity.defaultazurecredential) . В следующем примере кода показано, как получить учетные данные маркера, прошедшего проверку подлинности, и использовать его для создания объекта клиента службы, а затем использовать клиент службы для отправки нового большого двоичного объекта:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (StorageRequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Для авторизации запросов к данным большого двоичного объекта или очереди с помощью Azure AD необходимо использовать протокол HTTPS для этих запросов.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о ролях RBAC для службы хранилища Azure см. в статье [Управление правами доступа к данным хранилища с помощью RBAC](storage-auth-aad-rbac.md).
- Чтобы узнать об авторизации доступа к контейнерам и очередям из приложений службы хранилища, ознакомьтесь с разделом [Authenticate with Azure Active Directory from an Azure Storage application (Preview)](storage-auth-aad-app.md) (Аутентификация с помощью Azure Active Directory из приложения службы хранилища Azure (предварительная версия)).
- Сведения о выполнении Azure CLI и команд PowerShell с учетными данными Azure AD см. в статье [запуск Azure CLI или команд PowerShell с учетными данными Azure AD для доступа к данным BLOB-объектов или очередей](storage-auth-aad-script.md).
