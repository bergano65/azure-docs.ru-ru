---
title: Запуск команд PowerShell с учетными данными Azure AD для доступа к данным blob или очереди
titleSuffix: Azure Storage
description: PowerShell поддерживает вешку с учетными данными Azure AD для выполнения команд на blob Хранилища Azure и в очередях данных. Маркер доступа предоставляется для сеанса и позволяет авторизовать вызов операций. Разрешения зависят от роли RBAC, назначенной директору безопасности Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 373b939ac63f31ccaf6a9f01fac92920e19074ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75553453"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Запуск команд PowerShell с учетными данными Azure AD для доступа к данным blob или очереди

Azure Storage предоставляет расширения для PowerShell, которые позволяют входить в систему и запускать команды сценариев с помощью учетных данных Azure Active Directory (Azure AD). При вхобьем в PowerShell с учетными данными Azure AD возвращается токен доступа OAuth 2.0. Этот маркер автоматически используется PowerShell для авторизации последующих операций данных против хранилища Blob или Очереди. Для поддерживаемых операций больше не требуется передавать ключ учетной записи или маркер SAS с помощью команды.

Можно присвоить разрешения данным blob и очереди директору безопасности Azure AD с помощью элемента управления доступом на основе ролей (RBAC). Для получения дополнительной информации о роли RBAC в хранилище Azure [см. Управление правами доступа к данным хранения Azure с помощью RBAC.](storage-auth-aad-rbac.md)

## <a name="supported-operations"></a>Поддерживаемые операции

Расширения хранилища Azure поддерживаются для операций с данными капли и очереди. Какие операции вы можете вызвать, зависит от разрешений, выданных директору безопасности Azure AD, с которым вы впишетесь в PowerShell. Разрешения на контейнеры или очереди хранилища Azure назначаются через RBAC. Например, если вам была назначена роль **читателя данных Blob,** можно запустить команды по сценарию, которые считывались данные из контейнера или очереди. Если вам была назначена роль **вкладчика данных Blob,** вы можете запускать команды по сценарию, которые считываются, пишут или удаляют контейнер или очередь или содержащиеся в них данные.

Для получения подробной информации о разрешениях, необходимых для [Call storage operations with OAuth tokens](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)каждой операции хранения Azure в контейнере или очереди, см.  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Вызов команд PowerShell с помощью учетных данных Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Для использования Azure PowerShell для вхотворения и выполнения последующих операций против Azure Storage с помощью учетных данных Azure AD создайте контекст хранения для ссылки на учетную запись хранилища и включите `-UseConnectedAccount` параметр.

В следующем примере показано, как создать контейнер в новой учетной записи хранения данных из Azure PowerShell с помощью учетных данных Azure AD. Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

1. Вопийте в учетную запись Azure с помощью команды [Connect-AzAccount:](/powershell/module/az.accounts/connect-azaccount)

    ```powershell
    Connect-AzAccount
    ```

    Для получения дополнительной информации о вхене в Azure с PowerShell, [см.](/powershell/azure/authenticate-azureps)

1. Создайте группу ресурсов Azure, позвонив в [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Создайте учетную запись хранения, позвонив в [New-AzStorageAccount.](/powershell/module/az.storage/new-azstorageaccount)

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Получите контекст учетной записи хранилища, который определяет новую учетную запись хранения, позвонив в [New-AzStorageContext.](/powershell/module/az.storage/new-azstoragecontext) При действии на учетную запись хранилища можно ссылаться на контекст, а не на повторное прохождение учетных данных. Включите `-UseConnectedAccount` параметр для вызова любых последующих операций данных с помощью учетных данных Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Перед созданием контейнера присвоите себе роль [вкладчика хранилища данных Blob.](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) Даже если вы являетесь владельцем учетной записи, вам нужны четкие разрешения для выполнения операций данных в отношении учетной записи хранилища. Для получения дополнительной информации о назначении ролей RBAC см. [Грант доступ к каплям Azure и данным очереди с RBAC на портале Azure.](storage-auth-aad-rbac.md)

    > [!IMPORTANT]
    > Распространение ролевых заданий RBAC может занять несколько минут.

1. Создайте контейнер, позвонив в [New-AzStorageContainer.](/powershell/module/az.storage/new-azstoragecontainer) Поскольку этот вызов использует контекст, созданный в предыдущих шагах, контейнер создается с помощью учетных данных Azure AD.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Дальнейшие действия

- [Используйте PowerShell для присвоения роли RBAC для доступа к данным о каплях и очередях](storage-auth-aad-rbac-powershell.md)
- [Разрешить доступ к данным о каплях и очередях с управляемыми идентификаторами для ресурсов Azure](storage-auth-aad-msi.md)
