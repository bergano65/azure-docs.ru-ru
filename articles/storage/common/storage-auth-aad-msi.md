---
title: Авторизация доступа к BLOB-объектам и очередям с помощью Azure Active Directory и управляемых удостоверений для ресурсов Azure в службе хранилища Azure
description: Хранилище BLOB-объектов и очередей Azure поддерживает авторизацию доступа к ресурсам с помощью Azure Active Directory и управляемых удостоверений для ресурсов Azure. Управляемые удостоверения для ресурсов Azure можно использовать для авторизации доступа к BLOB-объектам и очередям из приложений, работающих на виртуальных машинах Azure, в приложениях-функциях, масштабируемых наборах виртуальных машин и других.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bed661873b195694c2fd9b30b1d98a3ecf1fc8a4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671108"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Авторизация доступа к BLOB-объектам и очередям с Azure Active Directory и управляемыми удостоверениями для ресурсов Azure

Хранилище BLOB-объектов и очередей поддерживают проверку подлинности Azure Active Directory (Azure AD) с использованием [управляемых удостоверений для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md). Управляемые удостоверения для ресурсов Azure могут авторизовать доступ к данным большого двоичного объекта и очереди с помощью учетных данных Azure AD из приложений, работающих на виртуальных машинах Azure, приложений-функций, масштабируемых наборов виртуальных машин и других служб. Используя управляемые удостоверения для ресурсов Azure вместе с проверкой подлинности Azure AD, можно избежать хранения учетных данных в приложениях, выполняемых в облаке.  

В этой статье показано, как авторизовать доступ к данным большого двоичного объекта или очереди с помощью управляемого удостоверения из виртуальной машины Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Включение управляемых удостоверений на виртуальной машине

Прежде чем использовать управляемые удостоверения для ресурсов Azure для авторизации доступа к BLOB-объектам и очередям из виртуальной машины, сначала необходимо включить управляемые удостоверения для ресурсов Azure на виртуальной машине. Сведения о включении управляемых удостоверений для ресурсов Azure см. в одной из следующих статей.

- [портал Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Интерфейс командной строки Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Шаблон Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Клиентские библиотеки Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Предоставление разрешений управляемому удостоверению Azure AD

Чтобы авторизовать запрос к большому двоичному объекту или служба очередей из управляемого удостоверения в приложении службы хранилища Azure, сначала настройте параметры управления доступом на основе ролей (RBAC) для этого управляемого удостоверения. Служба хранилища Azure определяет роли RBAC, охватывающие разрешения для данных BLOB-объектов и очередей. Когда роль RBAC назначается управляемому удостоверению, управляемому удостоверению предоставляются разрешения на доступ к данным большого двоичного объекта или очереди в соответствующей области.

Дополнительные сведения о назначении ролей RBAC см. в одной из следующих статей:

- [Предоставление доступа к BLOB-объектам Azure и создание очереди данных с использованием RBAC на портале Azure](storage-auth-aad-rbac-portal.md)
- [Предоставление доступа к BLOB-объектам Azure и создание очереди данных с использованием RBAC с помощью Azure CLI](storage-auth-aad-rbac-cli.md)
- [Предоставление доступа к BLOB-объектам Azure и создание очереди данных с использованием RBAC с помощью Powershell](storage-auth-aad-rbac-powershell.md)

## <a name="azure-storage-resource-id"></a>Идентификатор ресурса хранилища Azure

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Пример кода .NET. Создание блочного BLOB-объекта

В примере кода показано, как получить маркер OAuth 2,0 из Azure AD и использовать его для авторизации запроса на создание блочного BLOB-объекта. Чтобы получить этот пример, сначала выполните действия, описанные в предыдущих разделах.

[!INCLUDE [storage-app-auth-lib-include](../../../includes/storage-app-auth-lib-include.md)]

### <a name="add-the-callback-method"></a>Добавление метода обратного вызова

Метод обратного вызова проверяет срок действия маркера и при необходимости обновляет его.

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    // Note that you can also specify the root URI for your storage account as the resource ID.
    const string StorageResource = "https://storage.azure.com/";  

    // Use the same token provider to request a new token.
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource);

    // Renew the token 5 minutes before it expires.
    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);
    if (next.Ticks < 0)
    {
        next = default(TimeSpan);
        Console.WriteLine("Renewing token...");
    }

    // Return the new token and the next refresh time.
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```

### <a name="get-a-token-and-create-a-block-blob"></a>Получение маркера и создание блочного BLOB-объекта

Библиотека проверки подлинности приложений предоставляет класс **AzureServiceTokenProvider** . Экземпляр этого класса можно передать обратному вызову, который получает маркер и затем обновляет маркер до истечения срока его действия.

Следующий пример получает маркер и использует его для создания нового большого двоичного объекта, а затем использует тот же токен для чтения большого двоичного объекта.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = await TokenRenewerAsync(azureServiceTokenProvider,CancellationToken.None);

// Create storage credentials using the initial token, and connect the callback function
// to renew the token just before it expires
TokenCredential tokenCredential = new TokenCredential(tokenAndFrequency.Token,
                                                        TokenRenewerAsync,
                                                        azureServiceTokenProvider,
                                                        tokenAndFrequency.Frequency.Value);

StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a blob using the storage credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName),
                                            storageCredentials);

// Upload text to the blob.
await blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage.
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}",
                        DateTimeOffset.UtcNow,
                        await blob.DownloadTextAsync());

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

Дополнительные сведения о библиотеке проверки подлинности приложений см. [в статье Проверка подлинности между службами для Azure Key Vault с помощью .NET](../../key-vault/service-to-service-authentication.md).

Дополнительные сведения о получении маркера доступа см. в статье [Использование управляемых удостоверений для ресурсов Azure на виртуальной машине Azure для получения маркера доступа](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> Для авторизации запросов к данным большого двоичного объекта или очереди с помощью Azure AD необходимо использовать протокол HTTPS для этих запросов.

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о ролях RBAC для службы хранилища Azure см. в статье [Управление правами доступа к данным хранилища с помощью RBAC](storage-auth-aad-rbac.md).
- Чтобы узнать об авторизации доступа к контейнерам и очередям из приложений службы хранилища, ознакомьтесь с разделом [Authenticate with Azure Active Directory from an Azure Storage application (Preview)](storage-auth-aad-app.md) (Аутентификация с помощью Azure Active Directory из приложения службы хранилища Azure (предварительная версия)).
- Сведения о выполнении Azure CLI и команд PowerShell с учетными данными Azure AD см. в статье [запуск Azure CLI или команд PowerShell с учетными данными Azure AD для доступа к данным BLOB-объектов или очередей](storage-auth-aad-script.md).
