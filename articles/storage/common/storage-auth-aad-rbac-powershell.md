---
title: Управлять правами доступа Azure AD для данных больших двоичных объектов и очереди с помощью RBAC - хранилища Azure с помощью Azure PowerShell
description: Используйте Azure PowerShell для предоставления доступа к контейнерам и очереди с помощью управления доступом на основе ролей (RBAC). Хранилище Azure поддерживает встроенные и пользовательские роли RBAC для проверки подлинности с помощью Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: e850b915cd01b6bacd70d6df7752eeb83f7101d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153859"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-powershell"></a>Предоставление доступа к Azure данные больших двоичных объектов и очереди с помощью RBAC с помощью PowerShell

Azure Active Directory (Azure AD) разрешает права доступа к защищенным ресурсам с помощью [управления доступом на основе ролей (RBAC)](../../role-based-access-control/overview.md). Служба хранилища Azure определяет набор встроенных ролей RBAC, которые охватывают общие наборы разрешений, используемые для доступа к контейнерам или очередям. 

При назначении роли RBAC для субъекта безопасности Azure AD, Azure предоставляет доступ к этим ресурсам для этого участника безопасности. Доступ может ограничиваться уровнем подписки, группой ресурсов, учетной записью хранения или отдельным контейнером или очередью. Субъект безопасности Azure AD может быть пользователь, группы, субъекта-службы приложения, или [управляемое удостоверение для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md).

В этой статье описывается, как использовать Azure PowerShell, чтобы получить список встроенных ролей RBAC и назначить их пользователям. Дополнительные сведения об использовании Azure PowerShell см. в разделе [Общие сведения об Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Роли RBAC для больших двоичных объектов и очередей

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Определение области действия ресурсов 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Список доступных ролей RBAC

Чтобы получить список доступных встроенных ролей RBAC с помощью Azure PowerShell, используйте [Get AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) команды:

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Вы увидите встроенные роли данных службы хранилища Azure, в списке, а также другие встроенные роли для Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-user"></a>Назначение роли RBAC для пользователя

Чтобы назначить пользователю роль RBAC, используйте [New AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) команды. Формат команды могут отличаться в зависимости от области назначения. Следующие примеры показывают, как назначение роли пользователю в различных областях.

### <a name="container-scope"></a>Область контейнера

Чтобы назначить роль в контейнер с заданной областью, укажите строку, содержащую область контейнера для `--scope` параметра. Область для контейнера имеет следующий формат:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

В следующем примере назначается **участник для данных больших двоичных объектов хранилища** роли к пользователю, областью действия на контейнер с именем *образец контейнера*. Обязательно замените примеры значений и значения заполнителей в квадратные скобки, своими собственными значениями: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Области очереди

Чтобы назначить роль области очереди, укажите строку, содержащую область в очередь для `--scope` параметра. Область для очереди имеет следующий формат:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

В следующем примере назначается **участник для данных очереди хранилища** роли к пользователю, с заданной областью в очередь с именем *пример очереди*. Обязательно замените примеры значений и значения заполнителей в квадратные скобки, своими собственными значениями: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Область действия учетной записи хранения

Чтобы назначить роль, в учетную запись хранения с заданной областью, укажите область ресурс учетной записи хранения для `--scope` параметра. Область для учетной записи хранения указывается в формате:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

В следующем примере показан как область **модуль чтения данных хранилища BLOB-объектов** роли для пользователя на уровне учетной записи хранения. Обязательно замените примеры значений своими собственными значениями: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Области действия группы ресурсов

Чтобы назначить пользователю роль, в пределах группы ресурсов, укажите имя группы ресурсов или идентификатор для `--resource-group` параметра. В следующем примере назначается **модуль чтения данных очереди хранилища** роли для пользователя на уровне группы ресурсов. Обязательно замените примеры значений и значения-заполнители в скобках собственными значениями: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Область подписки

Чтобы назначить роль, в пределах подписки, укажите область для подписки в `--scope` параметра. Область для подписки имеет следующий формат:

```
/subscriptions/<subscription>
```

В следующем примере показано, как назначить **модуль чтения данных хранилища BLOB-объектов** роли для пользователя на уровне учетной записи хранения. Обязательно замените примеры значений своими собственными значениями: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Дальнейшие действия

- [Управление доступом с помощью RBAC и Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Предоставление доступа к Azure данные больших двоичных объектов и очереди с помощью RBAC с помощью Azure CLI](storage-auth-aad-rbac-cli.md)
- [Предоставление доступа к BLOB-объектов и очереди данных Azure с помощью RBAC на портале Azure](storage-auth-aad-rbac-portal.md)
