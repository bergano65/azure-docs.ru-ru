---
title: Используйте PowerShell для присвоения роли RBAC для доступа к данным
titleSuffix: Azure Storage
description: Узнайте, как использовать PowerShell для присвоения разрешений директору службы безопасности Active Directory с помощью элемента управления доступом на основе ролей (RBAC). Azure Storage поддерживает встроенные и пользовательские роли RBAC для проверки подлинности через Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1413035c879198cf333aeeb5d8fe993162939172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460579"
---
# <a name="use-powershell-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Используйте PowerShell для присвоения роли RBAC для доступа к данным о каплях и очередях

Azure Active Directory (Azure AD) разрешает права доступа к защищенным ресурсам с помощью [управления доступом на основе ролей (RBAC)](../../role-based-access-control/overview.md). Служба хранилища Azure определяет набор встроенных ролей RBAC, которые охватывают общие наборы разрешений, используемые для доступа к контейнерам или очередям.

Когда роль RBAC назначается директору безопасности Azure AD, Azure предоставляет доступ к этим ресурсам для этого принципа безопасности. Доступ может ограничиваться уровнем подписки, группой ресурсов, учетной записью хранения или отдельным контейнером или очередью. Директором безопасности Azure AD может быть пользователь, группа, директор службы приложений или [управляемый интимент для ресурсов Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

В этой статье описывается, как использовать Azure PowerShell для списка встроенных ролей RBAC и их присвоения пользователям. Для получения дополнительной информации об [использовании](https://docs.microsoft.com/powershell/azure/overview)Azure PowerShell см.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Роли RBAC для больших двоичных объектов и очередей

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Определение сферы охвата ресурсов

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Список доступных ролей RBAC

Чтобы перечислить доступные встроенные роли RBAC с Azure PowerShell, используйте команду [Get-AzRoleDefinition:](/powershell/module/az.resources/get-azroledefinition)

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Вы увидите встроенные роли данных данных хранилища Azure, а также другие встроенные роли для Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Назначить роль RBAC директору службы безопасности

Чтобы назначить роль RBAC директору безопасности, используйте команду [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment) Формат команды может отличаться в зависимости от объема назначения. Для выполнения команды необходимо назначить роль Владельца или Автора в соответствующем объеме. Ниже приведены следующие примеры, как назначить роль пользователю в различных областях, но можно использовать одну и ту же команду, чтобы назначить роль любому директору безопасности.

### <a name="container-scope"></a>Область контейнеров

Чтобы присвоить область действия контейнеру, укажите строку, содержащую область контейнера `--scope` для параметра. Область для контейнера в форме:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

Следующий пример присваивает пользователю роль **вкладчика хранилища данных Blob,** присваиваемую контейнеру с именем *образец-контейнер.* Убедитесь в том, чтобы заменить значения образца и заполнителя в скобках с вашими собственными значениями: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Область очереди

Чтобы присвоить область действия роли очереди, укажите строку, `--scope` содержащую область очереди для параметра. Область для очереди находится в форме:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

Следующий пример присваивает пользователю роль **вкладчика очереди хранения данных,** пригодивую к очереди с именем *выборки.* Убедитесь в том, чтобы заменить значения образца и заполнителя в скобках с вашими собственными значениями: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Область хранения данных

Чтобы присвоить область действия учетной записи хранилища, укажите `--scope` область ресурса учетной записи хранилища для параметра. Область для учетной записи хранилища находится в форме:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

В следующем примере показано, как передать роль **читателя данных Storage Blob** Reader пользователю на уровне учетной записи хранилища. Убедитесь в том, чтобы заменить значения выборки на свои собственные значения: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Область ресурсной группы

Чтобы присвоить группе ресурсов область действия роли, укажите `--resource-group` имя группы ресурсов или идентификатор для параметра. Следующий пример присваивает пользователю роль **чтения данных очереди хранения** данных на уровне группы ресурсов. Убедитесь в том, чтобы заменить образец значения и значения заполнителя в скобках с вашими собственными значениями: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Сфера действия подписки

Чтобы назначить роль, область действия подписки, укажите `--scope` область подписки для параметра. Область подписки в форме:

```
/subscriptions/<subscription>
```

В следующем примере показано, как назначить роль **читателя данных хранилища** для пользователей на уровне учетной записи хранилища. Убедитесь в том, чтобы заменить значения выборки на свои собственные значения: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Дальнейшие действия

- [Управление доступом с помощью RBAC и Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Предоставление доступа к BLOB-объектам Azure и создание очереди данных с использованием RBAC с помощью Azure CLI](storage-auth-aad-rbac-cli.md)
- [Предоставление доступа к BLOB-объектам Azure и создание очереди данных с использованием RBAC на портале Azure](storage-auth-aad-rbac-portal.md)
