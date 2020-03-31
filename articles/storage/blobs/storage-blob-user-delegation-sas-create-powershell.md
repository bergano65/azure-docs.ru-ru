---
title: Используйте PowerShell для создания делегации пользователя SAS для контейнера или капли
titleSuffix: Azure Storage
description: Узнайте, как создать SAS-делегации пользователей с помощью учетных данных Active Directory С помощью PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 5250a27e6c5fcf012207f1edb95ad46c0aabfe63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536179"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Создание делегации пользователя SAS для контейнера или капли с Помощью PowerShell

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

В этой статье показано, как использовать учетные данные Active Directory (Azure AD) для создания SAS-сообщества пользователей для контейнера или капли с Azure PowerShell.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>Установка модуля PowerShell

Чтобы создать делегацию пользователей SAS с PowerShell, установите версию 1.10.0 или более позднюю модуль Az.Storage. Выполните следующие действия, чтобы установить последнюю версию модуля:

1. Удалите все ранее установленные версии Azure PowerShell.

    - Удалите все предыдущие установки Azure PowerShell из Windows с помощью параметра **Apps & features** (Приложения и компоненты) в разделе **Параметры**.
    - Удалите все модули **Azure** из `%Program Files%\WindowsPowerShell\Modules`.

1. Убедитесь, что у вас установлена последняя версия PowerShellGet. Откройте окно Windows PowerShell и выполните следующую команду, чтобы установить последнюю версию:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. После установки PowerShellGet закройте и снова откройте окно PowerShell.

1. Установите Azure PowerShell последней версии:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Убедитесь, что вы установили версию Azure PowerShell 3.2.0 или позже. Выполнить следующую команду для установки последней версии модуля Azure Storage PowerShell:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Закройте и снова откройте окно PowerShell.

Чтобы проверить, какая версия модуля Az.Storage установлена, запустите следующую команду:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Для получения дополнительной информации об установке Azure PowerShell [см.](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Вопием в Azure PowerShell с помощью Azure AD

Позвоните в команду [Connect-AzAccount,](/powershell/module/az.accounts/connect-azaccount) чтобы войти в учетную запись Azure AD:

```powershell
Connect-AzAccount
```

Для получения дополнительной информации о вхере в PowerShell, [см.](/powershell/azure/authenticate-azureps)

## <a name="assign-permissions-with-rbac"></a>Назначать разрешения с RBAC

Для создания делегации пользователей SAS из Azure PowerShell учетная запись Azure AD, используемая для вхушки в PowerShell, должна быть назначена роль, включавв в себя действия **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Это разрешение позволяет учетной записи Azure AD запросить *ключ делегации пользователя.* Ключ делегирования пользователя используется для подписания делегации пользователя SAS. Роль, обеспечивающая действие **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey,** должно быть отнесено на уровне учетной записи хранилища, группы ресурсов или подписки. Для получения дополнительной информации о разрешениях RBAC на **Assign permissions with RBAC** создание SAS представительства [Create a user delegation SAS](/rest/api/storageservices/create-user-delegation-sas)пользователей см.

Если у вас нет достаточных разрешений на присвоение ролей RBAC директору безопасности Azure AD, может потребоваться попросить владельца учетной записи или администратора назначить необходимые разрешения.

Следующий пример присваивает роль **вкладчика хранилища данных Blob,** которая включает в себя **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** действий. Роль приравнивается на уровне учетной записи хранилища.

Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Для получения дополнительной информации о встроенных ролях, включающие действия **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey,** [см.](../../role-based-access-control/built-in-roles.md)

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Используйте учетные данные Azure AD для обеспечения безопасности SAS

При создании делегации пользователей SAS с Azure PowerShell ключ для регистрации SAS создается для вас неявно. Время начала и время истечения времени, указанное для SAS, также используется в качестве времени начала и истечения времени для ключа делегации пользователя. 

Поскольку максимальный интервал, в течение которого действует ключ делегации пользователя, составляет 7 дней с даты начала, следует указать время истечения срока действия SAS, которое составляет 7 дней после начала. SAS является недействительным после истечения срока действия ключа делегации пользователя, поэтому SAS со сроком действия более 7 дней будет по-прежнему действителен только в течение 7 дней.

Для создания sAS-делегации пользователя для контейнера или капли с Azure PowerShell сначала создайте новый контекстный объект Azure Storage с указанием `-UseConnectedAccount` параметра. Параметр `-UseConnectedAccount` указывает, что команда создает объект контекста в учетной записи Azure AD, в которую вы входите.

Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Создание sAS-делегации пользователя для контейнера

Чтобы вернуть токен SAS делегации пользователя в контейнер, позвоните в команду [New-AzStorageContainerSASToken,](/powershell/module/az.storage/new-azstoragecontainersastoken) передав объект контекста Хранения Azure, созданный ранее.

Следующий пример возвращает токен SAS делегации пользователя sAS для контейнера. Не забудьте заменить значения заполнителя в скобках на свои собственные значения:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

Возврат токенов SAS будет аналогичен:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Создание делегации пользователя SAS для капли

Чтобы вернуть токен SAS для получения капли, позвоните в команду [New-AzStorageBlobSASToken,](/powershell/module/az.storage/new-azstorageblobsastoken) передав объект контекста хранения Azure, созданный ранее.

Следующий синтаксис возвращает делегацию пользователя SAS за каплей. Пример определяет `-FullUri` параметр, который возвращает blob URI с приложеным токеном SAS. Не забудьте заменить значения заполнителя в скобках на свои собственные значения:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

Делегация пользователя SAS URI вернулась будет аналогична:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Делегация пользователя SAS не поддерживает определение разрешений с политикой сохраненного доступа.

## <a name="revoke-a-user-delegation-sas"></a>Отозвать делегацию пользователей SAS

Чтобы отозвать делегацию пользователя SAS из Azure PowerShell, позвоните в команду **Revoke-AzStorageAccountUserDelegationDelegationKeys.** Эта команда аннулирует все ключи делегации пользователя, связанные с указанной учетной записью хранения. Любые подписи общего доступа, связанные с этими ключами, аннулируются.

Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Как ключевые ключевые функции делегации пользователя, так и назначения ролей RBAC кэшируются Хранилищем Azure, поэтому может быть задержка между тем, когда вы инициируете процесс отзыва, и когда существующая делегация пользователя SAS становится недействительной.

## <a name="next-steps"></a>Дальнейшие действия

- [Создание делегации пользователей SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Получите операцию ключ к делегированию пользователя](/rest/api/storageservices/get-user-delegation-key)
