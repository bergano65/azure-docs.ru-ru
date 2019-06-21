---
title: Авторизация доступа к BLOB-объектов и очередей с Azure Active Directory и управляемых удостоверений для ресурсов Azure — хранилища Azure
description: Хранилище BLOB-объектов и очередей Azure поддерживает авторизацию доступа к ресурсам с помощью Azure Active Directory и управляемых удостоверений для ресурсов Azure. Управляемые удостоверения для ресурсов Azure можно использовать для авторизации доступа к большим двоичным объектам и очередям из приложений, работающих в виртуальных машинах Azure, приложения-функции, масштабируемые наборы виртуальных машин и другим пользователям.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 23e1171a8757d021b8c6d38f90bdbf720014045f
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303417"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Авторизация доступа к BLOB-объектов и очередей с Azure Active Directory и управляемых удостоверений для ресурсов Azure

Хранилище BLOB-объектов и очередей поддерживают проверку подлинности Azure Active Directory (Azure AD) с использованием [управляемых удостоверений для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md). Управляемые удостоверения для ресурсов Azure можно авторизовать доступ к BLOB-объектов и данных очереди, используя учетные данные Azure AD из приложений, работающих в виртуальных машинах (ВМ), приложения-функции, масштабируемые наборы виртуальных машин и других служб. С помощью управляемых удостоверений для ресурсов Azure вместе с аутентификацией Azure AD, можно избежать хранения учетных данных с приложениями, которые выполняются в облаке.  

В этой статье показано, как авторизовать доступ к данным BLOB-объектов или очередей с помощью управляемого удостоверения виртуальной машины Azure. 

## <a name="enable-managed-identities-on-a-vm"></a>Включение управляемых удостоверений на виртуальной машине

Прежде, чем управляемые удостоверения для ресурсов Azure можно использовать для авторизации доступа к большим двоичным объектам и очередям из виртуальной Машины, необходимо сначала включить управляемые удостоверения для ресурсов Azure на виртуальной Машине. Сведения о включении управляемых удостоверений для ресурсов Azure см. в одной из следующих статей.

- [портал Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Интерфейс командной строки Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Шаблон Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Пакеты SDK для Azure](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Предоставление разрешений на работу с удостоверением Azure AD в управляемых

Для авторизации запроса к службе BLOB-объектов или очередей управляемое удостоверение приложения службы хранилища Azure, необходимо сначала настройте параметры управления ДОСТУПОМ на основе ролей доступа для этого управляемого удостоверения. Хранилище Azure определяет роли RBAC, которые охватывают разрешения для данных больших двоичных объектов и очередей. При назначении роли RBAC управляемое удостоверение, управляемое удостоверение будут даны эти полномочия для данных больших двоичных объектов или очередей в соответствующей области. 

Дополнительные сведения о назначении роли RBAC см. в одном из следующих статей:

- [Предоставление доступа к BLOB-объектов и очереди данных Azure с помощью RBAC на портале Azure](storage-auth-aad-rbac-portal.md)
- [Предоставление доступа к Azure данные больших двоичных объектов и очереди с помощью RBAC с помощью Azure CLI](storage-auth-aad-rbac-cli.md)
- [Предоставление доступа к Azure данные больших двоичных объектов и очереди с помощью RBAC с помощью PowerShell](storage-auth-aad-rbac-powershell.md)

## <a name="authorize-with-a-managed-identity-access-token"></a>Авторизация с маркером доступа управляемого удостоверения

Для авторизации запросов к хранилищу BLOB-объектов и очередей с помощью управляемого удостоверения, приложение или сценарий должен получить маркер OAuth. [Проверки подлинности Microsoft Azure приложения](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) клиентскую библиотеку для .NET (Предварительная версия) упрощает процесс получения и продлевается токен из кода.

Клиентская библиотека проверки подлинности приложения автоматически управляет проверкой подлинности. Библиотека использует учетные данные разработчика для проверки подлинности во время локальной разработки. Использовать учетные данные разработчика во время локального развертывания более безопасно, так как вам не нужно создавать учетные данные Azure AD или совместно использовать учетные данные с другими разработчиками. Если решение позже развертывается в Azure, библиотека автоматически переключается на с использованием учетных данных приложения.

Чтобы использовать библиотеку проверки подлинности приложения в приложение хранилища Azure, установите последнюю версию пакета предварительной версии из [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication), а также последнюю версию [распространенных клиентской библиотеки хранилища Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) и [клиентская библиотека хранилища BLOB-объектов Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). Добавьте следующий **с помощью** инструкций в код:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

Библиотека проверки подлинности приложение предоставляет **AzureServiceTokenProvider** класса. Экземпляр этого класса может быть передан обратный вызов, который получает маркер, а затем обновляет маркер, до истечения срока его действия.

Следующий пример получает маркер и использует его для создания нового большого двоичного объекта, а затем использует тот же токен для чтения большого двоичного объекта.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = TokenRenewerAsync(azureServiceTokenProvider, 
                                            CancellationToken.None).GetAwaiter().GetResult();

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
blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage. 
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}", 
                        DateTimeOffset.UtcNow, 
                        blob.DownloadTextAsync().Result);

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

Метод обратного вызова проверяет время истечения срока действия маркера и обновит ее при необходимости:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
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

Дополнительные сведения о библиотеке проверки подлинности приложения, см. в разделе [проверки подлинности в службе в Azure Key Vault с помощью .NET](../../key-vault/service-to-service-authentication.md). 

Дополнительные сведения о том, как получить маркер доступа, см. в разделе [способы использования управляемых удостоверений для ресурсов Azure на виртуальной Машине Azure для получения маркеров доступа](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> Для авторизации запросов к данным BLOB-объектов или очередей в Azure AD, необходимо использовать HTTPS для этих запросов.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о ролях RBAC для службы хранилища Azure, см. в разделе [управление права доступа к данным хранилища с помощью RBAC](storage-auth-aad-rbac.md).
- Чтобы узнать об авторизации доступа к контейнерам и очередям из приложений службы хранилища, ознакомьтесь с разделом [Authenticate with Azure Active Directory from an Azure Storage application (Preview)](storage-auth-aad-app.md) (Аутентификация с помощью Azure Active Directory из приложения службы хранилища Azure (предварительная версия)).
- Чтобы узнать, как для выполнения команд Azure CLI и PowerShell учетные данные Azure AD, см. в разделе [команды запуска интерфейса командной строки Azure или PowerShell учетные данные Azure AD для доступа к данным BLOB-объектов или очередей](storage-auth-aad-script.md).