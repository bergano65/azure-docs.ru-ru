---
title: Выполните команды Azure CLI или PowerShell под удостоверением Azure AD для доступа к данным BLOB-объектов и очередей | Документация Майкрософт
description: Azure CLI и PowerShell поддерживают входа с использованием удостоверения Azure AD для выполнения команд в источнике данных больших двоичных объектов и очереди хранилища Azure. Маркер доступа предоставляется для сеанса и позволяет авторизовать вызов операций. Разрешения зависят от роли RBAC, назначенной удостоверения Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/26/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: a0972beff48e07b6ce8afdcec10581300f59ed41
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787004"
---
# <a name="use-an-azure-ad-identity-to-access-blob-and-queue-data-with-cli-or-powershell"></a>Использование удостоверения Azure AD для доступа к данным больших двоичных объектов и очереди с помощью интерфейса командной строки или PowerShell

Служба хранилища Azure предоставляет расширения для Azure CLI и PowerShell, которые позволяют выполнять вход и запустить команды сценария под удостоверением Azure Active Directory (Azure AD). Вы можете использовать удостоверение Azure AD пользователя, группы или субъекта-службы приложения, а также [управляемое удостоверение службы для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md). Можно назначить разрешения для доступа к данным BLOB-объектов и очередей для удостоверения Azure AD с помощью управления доступом на основе ролей (RBAC). Дополнительные сведения о ролях RBAC в службе хранилища Azure см. в разделе [управление права доступа к данным службы хранилища Azure с помощью RBAC](storage-auth-aad-rbac.md).

При входе Azure CLI или PowerShell с помощью удостоверения Azure AD, для доступа к службе хранилища Azure под этим удостоверением возвращается маркер доступа. Этот маркер затем автоматически используется интерфейсом командной строки или PowerShell для авторизации операций в службе хранилища Azure. Для поддерживаемых операций больше не требуется передавать ключ учетной записи или маркер SAS с помощью команды.

## <a name="supported-operations"></a>Поддерживаемые операции

Расширения поддерживаются для операций с контейнерами и очередей. Операции, которые могут вызвать зависит от разрешений, предоставленных удостоверения Azure AD, с которым входе в Azure CLI или PowerShell. Разрешения для контейнеров и очередей службы хранилища Azure назначаются с помощью управления доступом на основе ролей (RBAC). Например, если удостоверению назначена роль для чтения данных, то с его помощью можно выполнять команды сценария, которые считывают данных из очереди или контейнера. Если удостоверению назначена роль участника для данных, то с его помощью можно выполнять команды сценария, которые считывают, записывают или удаляют данные в контейнере или очереди. 

Дополнительные сведения о разрешениях, необходимых для каждой операции службы хранилища Azure с контейнером или очередью, представлены в разделе [Permissions for calling REST operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations) (Разрешения для вызова операций REST).  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Вызов команды интерфейса командной строки, используя учетные данные Azure AD

Azure CLI поддерживает `--auth-mode` параметра для операции с данными больших двоичных объектов и очереди:

- Задайте `--auth-mode` параметр `login` входить с помощью субъекта безопасности Azure AD.
- Задайте для параметра `--auth-mode` устаревшее значение `key`, чтобы выполнить попытку запроса ключа учетной записи, если не указаны параметры аутентификации для учетной записи. 

Приведенный ниже показано, как создать контейнер в учетную запись хранения с помощью Azure CLI с использованием учетных данных Azure AD. Не забудьте заменить значения-заполнители в угловых скобках собственными значениями: 

1. Убедитесь в том, что вы установили Azure CLI версии 2.0.46 или более поздней версии. Выполните команду `az --version`, чтобы узнать установленную версию.

1. Запустите `az login` и пройти проверку подлинности в окне браузера: 

    ```azurecli
    az login
    ```
    
1. Укажите нужный подписки. Создайте группу ресурсов, используя команду [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Создайте учетную запись в группу ресурсов, с помощью [Создание учетной записи хранения az](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create): 

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
    
1. Перед созданием контейнера, назначить [участник для данных больших двоичных объектов хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) роли себе. Несмотря на то, что вы являетесь владельцем учетной записи, вы должны явные разрешения для выполнения операций с данными в учетной записи хранения. Дополнительные сведения о назначении роли RBAC см. в разделе [предоставить доступ к BLOB-объектов и очереди данных Azure с помощью RBAC на портале Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Назначения ролей RBAC может занять несколько минут для распространения.
    
1. Вызовите [создать контейнер хранилища az](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) с `--auth-mode` параметру присвоить `login` для создания контейнера, используя свои учетные данные Azure AD:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

С параметром `--auth-mode` связана переменная среды `AZURE_STORAGE_AUTH_MODE`. Можно указать соответствующее значение в переменной среды, не добавляйте его при каждом вызове операции службы хранилища Azure данные.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Вызов команды PowerShell, используя учетные данные Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Чтобы использовать Azure PowerShell для входа и выполнять последующие операции в службе хранилища Azure, используя учетные данные Azure AD, создать контекст хранилища для ссылки на учетную запись хранения и включая `-UseConnectedAccount` параметра.

Приведенный ниже показано, как создать контейнер в учетную запись хранения с помощью Azure PowerShell с использованием учетных данных Azure AD. Не забудьте заменить значения-заполнители в угловых скобках собственными значениями:

1. Войдите в подписку Azure с помощью `Connect-AzAccount` команды и следуйте инструкциям на экране инструкциям, чтобы ввести свои учетные данные Azure AD: 

    ```powershell
    Connect-AzAccount
    ```
    
1. Создайте группу ресурсов Azure, вызвав [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Создайте учетную запись хранилища, вызвав [New AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Получите контекст учетной записи хранения, который указывает новую учетную запись хранения, вызвав [New AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Действуя в учетной записи хранения может ссылаться на контекст, вместо того чтобы постоянно передавать учетные данные. Включить `-UseConnectedAccount` параметр для вызова любых операциях последующих данных, используя свои учетные данные Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Перед созданием контейнера, назначить [участник для данных больших двоичных объектов хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) роли себе. Несмотря на то, что вы являетесь владельцем учетной записи, вы должны явные разрешения для выполнения операций с данными в учетной записи хранения. Дополнительные сведения о назначении роли RBAC см. в разделе [предоставить доступ к BLOB-объектов и очереди данных Azure с помощью RBAC на портале Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Назначения ролей RBAC может занять несколько минут для распространения.

1. Создайте контейнер путем вызова [New AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Так как этот вызов использует контекст, созданные на предыдущих шагах, создании контейнера, используя свои учетные данные Azure AD. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о ролях RBAC для службы хранилища Azure, см. в разделе [управление права доступа к данным хранилища с помощью RBAC](storage-auth-aad-rbac.md).
- Дополнительные сведения об использовании управляемых удостоверений для ресурсов Azure со службой хранилища Azure, см. в разделе [проверка подлинности доступа к BLOB-объектов и очередей с помощью Azure управляемые удостоверения для ресурсов Azure](storage-auth-aad-msi.md).
- Чтобы узнать об авторизации доступа к контейнерам и очередям из приложений службы хранилища, ознакомьтесь с разделом [Authenticate with Azure Active Directory from an Azure Storage application (Preview)](storage-auth-aad-app.md) (Аутентификация с помощью Azure Active Directory из приложения службы хранилища Azure (предварительная версия)).
