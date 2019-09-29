---
title: Использование Azure CLI для управления правами доступа Azure AD к данным BLOB-объектов и очередей с помощью RBAC в службе хранилища Azure
description: Используйте Azure CLI, чтобы назначить доступ к контейнерам и очередям с помощью управления доступом на основе ролей (RBAC). Служба хранилища Azure поддерживает встроенные и настраиваемые роли RBAC для проверки подлинности с помощью Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 606dd88fbad8cbd5c7e24d47dcf71199a25b49a2
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673206"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-azure-cli"></a>Предоставление доступа к данным большого двоичного объекта и очереди Azure с помощью RBAC с использованием Azure CLI

Azure Active Directory (Azure AD) разрешает права доступа к защищенным ресурсам с помощью [управления доступом на основе ролей (RBAC)](../../role-based-access-control/overview.md). Служба хранилища Azure определяет набор встроенных ролей RBAC, охватывающих общие наборы разрешений, используемых для доступа к данным BLOB-объектов или очередей.

Когда роль RBAC назначается субъекту безопасности Azure AD, Azure предоставляет доступ к этим ресурсам для этого субъекта безопасности. Доступ может ограничиваться уровнем подписки, группой ресурсов, учетной записью хранения или отдельным контейнером или очередью. Субъект безопасности Azure AD может быть пользователем, группой, субъектом-службой приложения или [управляемым удостоверением для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md).

В этой статье описывается, как использовать Azure CLI для перечисления встроенных ролей RBAC и их назначения пользователям. Дополнительные сведения об использовании Azure CLI см. в разделе [интерфейс командной строки Azure (CLI)](https://docs.microsoft.com/cli/azure).

## <a name="rbac-roles-for-blobs-and-queues"></a>Роли RBAC для больших двоичных объектов и очередей

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Определение области действия ресурса 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Вывод списка доступных ролей RBAC

Чтобы получить список доступных встроенных ролей RBAC с Azure CLI, используйте команду [AZ Role Definition List](/cli/azure/role/definition#az-role-definition-list) .

```azurecli-interactive
az role definition list --out table
```

Вы увидите список встроенных ролей данных службы хранилища Azure вместе с другими встроенными ролями Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Назначение роли RBAC субъекту безопасности

Чтобы назначить роль RBAC субъекту безопасности, используйте команду [AZ Role назначение Create](/cli/azure/role/assignment#az-role-assignment-create) . Формат команды может различаться в зависимости от области назначения. В следующих примерах показано, как назначить роль пользователю в различных областях, но можно использовать ту же команду, чтобы назначить роль любому субъекту безопасности.

### <a name="container-scope"></a>Область контейнера

Чтобы назначить роль для контейнера, укажите строку, содержащую область действия контейнера для параметра `--scope`. Область для контейнера имеет вид:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

В следующем примере роль **участника данных BLOB-объекта хранилища** назначается пользователю в области действия уровня контейнера. Обязательно замените образцы значений и значения заполнителей в квадратных скобках собственными значениями:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Область очереди

Чтобы назначить роль, ограниченную очередью, укажите строку, содержащую область очереди, для параметра `--scope`. Область для очереди имеет вид:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

В следующем примере роль **участника данных очереди хранилища** назначается пользователю в области действия уровня очереди. Обязательно замените образцы значений и значения заполнителей в квадратных скобках собственными значениями:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Область учетной записи хранения

Чтобы назначить роль, ограниченную учетной записью хранения, укажите область ресурса учетной записи хранения для параметра `--scope`. Область для учетной записи хранения имеет вид:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

В следующем примере показано, как назначить роль **модуля чтения данных большого двоичного объекта хранилища** пользователю на уровне учетной записи хранения. Обязательно замените образцы значений собственными значениями: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Область группы ресурсов

Чтобы назначить роль, ограниченную группой ресурсов, укажите имя или идентификатор группы ресурсов для параметра `--resource-group`. В следующем примере роль **модуля чтения данных очереди хранилища** назначается пользователю на уровне группы ресурсов. Не забудьте заменить образцы значений и значения заполнителей в квадратных скобках собственными значениями:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Область действия подписки

Чтобы назначить роль, ограниченную подпиской, укажите область для подписки для параметра `--scope`. Область для подписки имеет вид:

```
/subscriptions/<subscription>
```

В следующем примере показано, как назначить роль **модуля чтения данных большого двоичного объекта хранилища** пользователю на уровне учетной записи хранения. Обязательно замените образцы значений собственными значениями: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Следующие шаги

- [Управление доступом с помощью RBAC и Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Предоставление доступа к данным большого двоичного объекта и очереди Azure с помощью RBAC с использованием Azure PowerShell](storage-auth-aad-rbac-powershell.md)
- [Предоставление доступа к BLOB-объектам Azure и создание очереди данных с использованием RBAC на портале Azure](storage-auth-aad-rbac-portal.md)
