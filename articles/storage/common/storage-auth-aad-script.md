---
title: Выполните команды Azure CLI или PowerShell учетные данные Azure AD для доступа к данным BLOB-объектов или очередей | Документация Майкрософт
description: Azure CLI и PowerShell поддерживают вход с использованием учетных данных Azure AD для выполнения команд в источнике данных больших двоичных объектов и очереди хранилища Azure. Маркер доступа предоставляется для сеанса и позволяет авторизовать вызов операций. Разрешения зависят от роли RBAC, назначенный для субъекта безопасности Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 3fe142439dc80af660d286e5913fee13d4de8e86
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625660"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Выполните команды Azure CLI или PowerShell учетные данные Azure AD для доступа к данным BLOB-объектов или очередей

Служба хранилища Azure предоставляет расширения для Azure CLI и PowerShell, которые позволяют выполнять вход и выполнения команды сценария с учетными данными Azure Active Directory (Azure AD). При входе Azure CLI или PowerShell учетные данные Azure AD, возвращается маркер доступа OAuth 2.0. Этот маркер используется автоматически CLI или PowerShell для авторизации последующих операций данных в службе хранилища BLOB-объектов или очередей. Для поддерживаемых операций больше не требуется передавать ключ учетной записи или маркер SAS с помощью команды.

Можно назначить разрешения для данных больших двоичных объектов и очереди для субъекта безопасности Azure AD с помощью управления доступом на основе ролей (RBAC). Дополнительные сведения о ролях RBAC в службе хранилища Azure см. в разделе [управление права доступа к данным службы хранилища Azure с помощью RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Поддерживаемые операции

Расширения поддерживаются для операций с контейнерами и очередей. Операции, которые могут вызвать зависит от разрешений, предоставленных участнику безопасности Azure AD, с которым входе в Azure CLI или PowerShell. Разрешения для контейнеров и очередей службы хранилища Azure назначаются с помощью управления доступом на основе ролей (RBAC). Например, если вам назначена **модуль чтения данных больших двоичных объектов** роли, то можно запустить команды сценария, которые считывают данные из контейнера или очереди. Если вам назначена **участник для данных больших двоичных объектов** роли, то можно запустить команды сценария, читать, записывать и удалять контейнер или очередь или они содержат данные. 

Дополнительные сведения о разрешениях, необходимых для каждой операции с хранилищем Azure в контейнере или очереди, см. в разделе [вызывать операции хранилища с помощью маркеров OAuth](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

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

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о ролях RBAC для службы хранилища Azure, см. в разделе [управление права доступа к данным хранилища с помощью RBAC](storage-auth-aad-rbac.md).
- Дополнительные сведения об использовании управляемых удостоверений для ресурсов Azure со службой хранилища Azure, см. в разделе [проверки подлинности доступа к BLOB-объектов и очередей с Azure Active Directory и управляемых удостоверений для ресурсов Azure](storage-auth-aad-msi.md).
- Чтобы узнать об авторизации доступа к контейнерам и очередям из приложений службы хранилища, ознакомьтесь с разделом [Authenticate with Azure Active Directory from an Azure Storage application (Preview)](storage-auth-aad-app.md) (Аутентификация с помощью Azure Active Directory из приложения службы хранилища Azure (предварительная версия)).
