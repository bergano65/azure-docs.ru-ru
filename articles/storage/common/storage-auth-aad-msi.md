---
title: Авторизация доступа к данным с помощью управляемого удостоверения
titleSuffix: Azure Storage
description: Узнайте, как использовать управляемые удостоверения для ресурсов Azure для авторизации доступа к данным BLOB-объектов и очередей из приложений, работающих на виртуальных машинах Azure, в приложениях-функциях, масштабируемых наборах виртуальных машин и других.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c0b07cc001f65ff369f87954779634b8b49ba4ec
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892057"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Авторизация доступа к данным BLOB-объектов и очередей с помощью управляемых удостоверений для ресурсов Azure

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

Клиентская библиотека удостоверений Azure предоставляет поддержку проверки подлинности маркеров Azure AD для [пакета Azure SDK](https://github.com/Azure/azure-sdk). Последние версии клиентских библиотек службы хранилища Azure для .NET, Java, Python и JavaScript интегрируются с библиотекой удостоверений Azure для предоставления простого и безопасного способа получения маркера OAuth 2,0 для авторизации запросов к службе хранилища Azure.

Преимущество клиентской библиотеки Azure Identity заключается в том, что она позволяет использовать тот же код для проверки подлинности при выполнении приложения в среде разработки или в Azure. Клиентская библиотека удостоверений Azure для .NET выполняет проверку подлинности субъекта безопасности. Когда код выполняется в Azure, участник безопасности является управляемым удостоверением для ресурсов Azure. В среде разработки управляемое удостоверение не существует, поэтому клиентская библиотека проверяет подлинность пользователя или субъекта-службы в целях тестирования.

После проверки подлинности клиентская библиотека удостоверений Azure получает учетные данные маркера. Затем эти учетные данные маркера инкапсулируются в объекте клиента службы, который вы создадите для выполнения операций с хранилищем Azure. Библиотека эффективно обрабатывает это, получая соответствующие учетные данные маркера.

Дополнительные сведения о клиентской библиотеке удостоверений Azure для .NET см. в статье [Клиентская библиотека удостоверений Azure для .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity). Справочную документацию по клиентской библиотеке удостоверений Azure см. в статье [пространство имен Azure. Identity](/dotnet/api/azure.identity).

### <a name="assign-role-based-access-control-rbac-roles-for-access-to-data"></a>Назначение ролей управления доступом на основе ролей (RBAC) для доступа к данным

Когда субъект безопасности Azure AD пытается получить доступ к данным большого двоичного объекта или очереди, этот субъект безопасности должен иметь разрешения для ресурса. Независимо от того, является ли участник безопасности управляемым удостоверением в Azure или учетной записью пользователя Azure AD, выполняющего код в среде разработки, участнику безопасности должна быть назначена роль RBAC, которая предоставляет доступ к данным большого двоичного объекта или очереди в службе хранилища Azure. Сведения о назначении разрешений через RBAC см. в разделе **назначение РОЛЕЙ RBAC для прав доступа** в статье [авторизация доступа к BLOB-объектам и очередям Azure с помощью Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

### <a name="authenticate-the-user-in-the-development-environment"></a>Проверка подлинности пользователя в среде разработки

Если код выполняется в среде разработки, проверка подлинности может выполняться автоматически, или в зависимости от используемых инструментов может потребоваться вход в браузер. Например, Microsoft Visual Studio поддерживает единый вход (SSO), чтобы активная учетная запись пользователя Azure AD автоматически использовалась для проверки подлинности. Дополнительные сведения об единого входа см. [в статье единый вход в приложения](../../active-directory/manage-apps/what-is-single-sign-on.md).

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

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Пример кода .NET: создание блочного BLOB-объекта

Добавьте в код следующие директивы `using` для использования Azure Identity и клиентских библиотек службы хранилища Azure.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
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
    catch (RequestFailedException e)
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

- [Управление правами доступа к данным хранилища с помощью RBAC](storage-auth-aad-rbac.md).
- [Используйте Azure AD с приложениями службы хранилища](storage-auth-aad-app.md).
- [Выполните Azure CLI или команды PowerShell с учетными данными Azure AD для доступа к данным большого двоичного объекта или очереди](storage-auth-aad-script.md).
