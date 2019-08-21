---
title: Запуск Azure CLI или команд PowerShell с учетными данными Azure AD для доступа к данным BLOB-объектов или очередей | Документация Майкрософт
description: Azure CLI и PowerShell поддерживают вход с использованием учетных данных Azure AD для выполнения команд в хранилище BLOB-объектов Azure и очередей данных. Маркер доступа предоставляется для сеанса и позволяет авторизовать вызов операций. Разрешения зависят от роли RBAC, назначенной субъекту безопасности Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 32b3f113658a20790e0f149739a882004f12fe21
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640824"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Запуск Azure CLI или команд PowerShell с учетными данными Azure AD для доступа к данным BLOB-объектов или очередей

Служба хранилища Azure предоставляет расширения для Azure CLI и PowerShell, которые позволяют выполнять команды создания скриптов с учетными данными Azure Active Directory (Azure AD). При входе в Azure CLI или PowerShell с учетными данными Azure AD возвращается маркер доступа OAuth 2,0. Этот маркер автоматически используется интерфейсом CLI или PowerShell для авторизации последующих операций с данными в хранилище BLOB-объектов или очередей. Для поддерживаемых операций больше не требуется передавать ключ учетной записи или маркер SAS с помощью команды.

Вы можете назначать разрешения для данных BLOB-объектов и очередей субъекту безопасности Azure AD через Управление доступом на основе ролей (RBAC). Дополнительные сведения о ролях RBAC в службе хранилища Azure см. в статье [Управление правами доступа к данным службы хранилища Azure с помощью RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Поддерживаемые операции

Расширения поддерживаются для операций с контейнерами и очередями. Действия, которые можно вызывать, зависят от разрешений, предоставленных субъекту безопасности Azure AD, с которым вы входите в Azure CLI или PowerShell. Разрешения для контейнеров и очередей службы хранилища Azure назначаются с помощью управления доступом на основе ролей (RBAC). Например, если назначена роль **читателя данных большого двоичного объекта** , можно выполнять команды сценариев, считывающие данные из контейнера или очереди. Если вам назначена роль **участника данных большого двоичного объекта** , можно выполнять команды сценариев, которые считывают, записывают или удаляют контейнер или очередь или содержащиеся в них данные. 

Дополнительные сведения о разрешениях, необходимых для каждой операции службы хранилища Azure в контейнере или очереди, см. в разделе [операции хранилища вызовов с токенами OAuth](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Вызов команд интерфейса командной строки с использованием учетных данных Azure AD

Azure CLI поддерживает `--auth-mode` параметр для операций с данными большого двоичного объекта и очереди:

- Задайте для `--auth-mode` `login` параметра значение, чтобы выполнить вход с помощью субъекта безопасности Azure AD.
- Задайте для параметра `--auth-mode` устаревшее значение `key`, чтобы выполнить попытку запроса ключа учетной записи, если не указаны параметры аутентификации для учетной записи. 

В следующем примере показано, как создать контейнер в новой учетной записи хранения из Azure CLI с помощью учетных данных Azure AD. Не забудьте заменить значения заполнителей в угловых скобках собственными значениями: 

1. Убедитесь, что установлен Azure CLI версии 2.0.46 или более поздней. Выполните команду `az --version`, чтобы узнать установленную версию.

1. Запустите `az login` и выполните аутентификацию в окне браузера: 

    ```azurecli
    az login
    ```

1. Укажите нужную подписку. Создайте группу ресурсов, используя команду [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Создайте учетную запись хранения в этой группе ресурсов с помощью команды [AZ Storage Account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create):

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```

1. Перед созданием контейнера назначьте себе роль [участника данных BLOB-объекта хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) . Несмотря на то, что вы являетесь владельцем учетной записи, вам нужны явные разрешения на выполнение операций с данными с учетной записью хранения. Дополнительные сведения о назначении ролей RBAC см. [в разделе Предоставление доступа к данным BLOB-объектов и очередей Azure с помощью RBAC в портал Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Назначений ролей RBAC может потребоваться несколько минут для распространения.

1. Чтобы создать контейнер с использованием учетных данных Azure `--auth-mode` AD, вызовите команду [AZ Storage Container Create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) с параметром, имеющим значение. `login`

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

С параметром `--auth-mode` связана переменная среды `AZURE_STORAGE_AUTH_MODE`. Можно указать соответствующее значение в переменной среды, чтобы не включать его при каждом вызове операции с данными службы хранилища Azure.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Вызов команд PowerShell с использованием учетных данных Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Чтобы использовать Azure PowerShell для входа и выполнения последующих операций в службе хранилища Azure с помощью учетных данных Azure AD, создайте контекст хранилища для ссылки на учетную запись хранения `-UseConnectedAccount` и включите параметр.

В следующем примере показано, как создать контейнер в новой учетной записи хранения из Azure PowerShell с помощью учетных данных Azure AD. Не забудьте заменить значения заполнителей в угловых скобках собственными значениями:

1. Войдите в свою учетную запись Azure с помощью команды [Connect-азаккаунт](/powershell/module/az.accounts/connect-azaccount) :

    ```powershell
    Connect-AzAccount
    ```

    Дополнительные сведения о входе в Azure с помощью PowerShell см. [в разделе Вход с помощью Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Создайте группу ресурсов Azure, вызвав командлет [New-азресаурцеграуп](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Создайте учетную запись хранения, вызвав командлет [New-азсторажеаккаунт](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Получите контекст учетной записи хранения, указывающий новую учетную запись хранения, вызвав [New-азсторажеконтекст](/powershell/module/az.storage/new-azstoragecontext). При работе с учетной записью хранения можно ссылаться на контекст, а не повторять передачу учетных данных. `-UseConnectedAccount` Включите параметр для вызова любых последующих операций с данными с использованием учетных данных Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Перед созданием контейнера назначьте себе роль [участника данных BLOB-объекта хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) . Несмотря на то, что вы являетесь владельцем учетной записи, вам нужны явные разрешения на выполнение операций с данными с учетной записью хранения. Дополнительные сведения о назначении ролей RBAC см. [в разделе Предоставление доступа к данным BLOB-объектов и очередей Azure с помощью RBAC в портал Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Назначений ролей RBAC может потребоваться несколько минут для распространения.

1. Создайте контейнер, вызвав [New-азсторажеконтаинер](/powershell/module/az.storage/new-azstoragecontainer). Так как этот вызов использует контекст, созданный на предыдущих шагах, контейнер создается с использованием учетных данных Azure AD. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о ролях RBAC для службы хранилища Azure см. в статье [Управление правами доступа к данным хранилища с помощью RBAC](storage-auth-aad-rbac.md).
- Дополнительные сведения об использовании управляемых удостоверений для ресурсов Azure в службе хранилища Azure см. в статье [Проверка подлинности доступа к BLOB-объектам и очередям с Azure Active Directory и управляемыми удостоверениями для ресурсов Azure](storage-auth-aad-msi.md).
- Чтобы узнать об авторизации доступа к контейнерам и очередям из приложений службы хранилища, ознакомьтесь с разделом [Authenticate with Azure Active Directory from an Azure Storage application (Preview)](storage-auth-aad-app.md) (Аутентификация с помощью Azure Active Directory из приложения службы хранилища Azure (предварительная версия)).
