---
title: Разрешить доступ к данным с управляемым удостоверением личности
titleSuffix: Azure Storage
description: Узнайте, как использовать управляемые идентификаторы для ресурсов Azure для авторизации доступа к каборам и данным очередей из приложений, работающих в виртуальных машинах Azure, функциональных приложениях, наборах виртуальных машин и других.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f3bac0d47a53da1ec4d1fa08b5f0933f5f65dc56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255344"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Разрешить доступ к данным о каплях и очередях с управляемыми идентификаторами для ресурсов Azure

Хранилище BLOB-объектов и очередей поддерживают проверку подлинности Azure Active Directory (Azure AD) с использованием [управляемых удостоверений для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md). Управляемые идентификаторы для ресурсов Azure могут авторизовать доступ к данным blob и очередей, используя учетные данные Azure AD из приложений, работающих в виртуальных машинах Azure (VMs), функциональных приложений, виртуальных наборов масштабов машин и других служб. Используя управляемые идентификаторы для ресурсов Azure вместе с аутентификацией Azure AD, можно избежать хранения учетных данных с помощью приложений, запускаемых в облаке.  

В этой статье показано, как авторизовать доступ к данным blob или очереди из Azure VM с помощью управляемых идентификаторов для ресурсов Azure. В нем также описывается, как тестировать код в среде разработки.

## <a name="enable-managed-identities-on-a-vm"></a>Включение управляемых удостоверений на виртуальной машине

Прежде чем использовать управляемые идентификаторы для ресурсов Azure для авторизации доступа к каплям и очередям из ВМ, необходимо сначала включить управляемые идентификаторы для ресурсов Azure на VM. Сведения о включении управляемых удостоверений для ресурсов Azure см. в одной из следующих статей.

- [Портал Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Лазурная силаШелл](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Лазурный CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Шаблон менеджера ресурсов Azure](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Клиентские библиотеки менеджера ресурсов Azure](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Для получения дополнительной информации об управляемых идентификаторах [см. Управляемые идентификаторы для ресурсов Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="authenticate-with-the-azure-identity-library"></a>Authenticate с библиотекой идентификации Azure

Библиотека клиентов Azure Identity предоставляет поддержку маркера аутенке тачек Azure Azure AD для [SDK Azure.](https://github.com/Azure/azure-sdk) Последние версии клиентских библиотек Azure Storage для .NET, Java, Python и JavaScript интегрируются с библиотекой идентификаторов Azure, чтобы обеспечить простое и безопасное средство для получения токена OAuth 2.0 для авторизации запросов на хранение Azure.

Преимущество клиентской библиотеки Azure Identity заключается в том, что она позволяет использовать тот же код для проверки подлинности того, работает ли приложение в среде разработки или в Azure. Библиотека клиентов Azure Identity для .NET проверяет подлинность принципала безопасности. Когда код работает в Azure, принцип безопасности является управляемым итемным для ресурсов Azure. В среде разработки управляемого удостоверения не существует, поэтому клиентская библиотека проверяет подлинность пользователя или директора службы для целей тестирования.

После проверки подлинности клиентская библиотека Azure Identity получает учетные данные маркеров. Этот учетный знак затем инкапсулируется в объекте клиента службы, который вы создаете для выполнения операций против Azure Storage. Библиотека обрабатывает это для вас бесшовно, получая соответствующие учетные данные маркеров.

Для получения дополнительной информации о клиентской библиотеке Azure Identity для .NET [см.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) Для справочной документации для клиентской библиотеки Azure Identity можно ознакомиться на [сайте Azure.Identity Namespace](/dotnet/api/azure.identity).

### <a name="assign-role-based-access-control-rbac-roles-for-access-to-data"></a>Назначать роли управления доступом на основе ролей (RBAC) для доступа к данным

Когда принцип безопасности Azure AD пытается получить доступ к данным blob или очереди, у этого принципала безопасности должны быть разрешения на ресурс. Независимо от того, является ли принцип безопасности управляемым удостоверением личности в Azure или учетной записью пользователя Azure AD, работающим с кодом в среде разработки, директору безопасности необходимо назначить роль RBAC, которая предоставляет доступ к каблу или данным очереди в Azure Storage. Для получения информации о назначении разрешений через RBAC см. раздел под названием **Присваивать роли RBAC для прав доступа** в [authorize access to Azure blobs и очередей с помощью Azure Active Directory.](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)

### <a name="authenticate-the-user-in-the-development-environment"></a>Authenticate пользователя в среде разработки

Когда код работает в среде разработки, аутентификация может обрабатываться автоматически, или это может потребовать входа в браузер, в зависимости от того, какие инструменты вы используете. Например, Microsoft Visual Studio поддерживает одиночный входе (SSO), так что активная учетная запись пользователя Azure AD автоматически используется для проверки подлинности. Для получения дополнительной информации о SSO, см [Единый знак на приложения](../../active-directory/manage-apps/what-is-single-sign-on.md).

Другие средства разработки могут побудить вас войти через веб-браузер.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Authenticate директор службы в среде разработки

Если среда разработки не поддерживает один вход или логин через веб-браузер, то вы можете использовать принцип службы для проверки подлинности из среды разработки.

#### <a name="create-the-service-principal"></a>Создание субъекта-службы

Чтобы создать основную службу с Azure CLI и назначить роль RBAC, позвоните в команду [az ad sp create-for-rbac.](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) Предоставьте роль доступа к данным хранилища Azure для присвоения новому главному сервису. Кроме того, предоставьте область действия для назначения ролей. Для получения дополнительной информации о встроенных ролях, предусмотренных для хранения Azure, [см.](../../role-based-access-control/built-in-roles.md)

Если у вас нет достаточного разрешения на назначение роли директору службы, возможно, потребуется попросить владельца учетной записи или администратора выполнить назначение роли.

В следующем примере используется ClI Azure для создания нового принципала службы и присваивания ему роль **чтения данных Storage Blob** с областью охвата учетной записи

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Команда `az ad sp create-for-rbac` возвращает список основных свойств службы в формате JSON. Скопируйте эти значения, чтобы можно было использовать их для создания необходимых переменных среды на следующем этапе.

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
> Распространение ролевых заданий RBAC может занять несколько минут.

#### <a name="set-environment-variables"></a>Настройка переменных среды

Библиотека клиентов Azure Identity считывает значения из трех переменных среды во время выполнения для проверки подлинности основного обслуживания. В следующей таблице описывается значение, установленное для каждой переменной среды.

|Переменная среды|Значение
|-|-
|`AZURE_CLIENT_ID`|Идентификатор приложения для директора службы
|`AZURE_TENANT_ID`|Идентификатор арендатора Azure AD директора службы
|`AZURE_CLIENT_SECRET`|Пароль, генерируемый для основного обслуживания

> [!IMPORTANT]
> После установки переменных среды закройте и повторно откройте окно консоли. Если вы используете Visual Studio или другую среду разработки, возможно, потребуется перезапустить среду разработки, чтобы зарегистрировать новые переменные среды.

Для получения дополнительной информации на портале можно [ознакомиться на портале «Создание иждивенного данных для приложения Azure](../../active-directory/develop/howto-create-service-principal-portal.md)».

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Пример кода .NET: создание блочного BLOB-объекта

Добавьте `using` в код следующие директивы, чтобы использовать клиентские библиотеки Azure Identity и Azure Storage.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Чтобы получить учетные данные маркеров, которые код может использовать для авторизации запросов в Хранилище Azure, создайте экземпляр класса [DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential) Следующий пример кода показывает, как получить проверенные учетные данные маркера и использовать его для создания объекта клиента службы, а затем использовать клиент службы для загрузки нового капли:

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
> Чтобы авторизовать запросы в отношении данных blob или очереди с помощью Azure AD, необходимо использовать HTTPS для этих запросов.

## <a name="next-steps"></a>Дальнейшие действия

- [Управление правами доступа к данным хранения с помощью RBAC.](storage-auth-aad-rbac.md)
- [Используйте Azure AD с приложениями для хранения данных.](storage-auth-aad-app.md)
- [Выполнить команды Azure CLI или PowerShell с учетными данными Azure AD для доступа к данным blob или очереди.](authorize-active-directory-powershell.md)
