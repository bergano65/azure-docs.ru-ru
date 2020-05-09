---
title: Вывод списка назначений ролей Azure с помощью Azure CLI — Azure RBAC
description: Узнайте, как определить, к каким ресурсам пользователи, группы, субъекты-службы или управляемые удостоверения имеют доступ с помощью Azure CLI и управления доступом на основе ролей Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f4b635d6867c36b8b0f385320e3720bea41b54d1
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735748"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Вывод списка назначений ролей Azure с помощью Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]В этой статье описывается, как вывести список назначений ролей с помощью Azure CLI.

> [!NOTE]
> Если в вашей организации есть функции управления с использованием внешнего источника для поставщика услуг, использующего [Управление делегированными ресурсами Azure](../lighthouse/concepts/azure-delegated-resource-management.md), назначения ролей, предоставленные этим поставщиком услуг, не будут показаны здесь.

## <a name="prerequisites"></a>Предварительные условия

- [Bash в Azure Cloud Shell](/azure/cloud-shell/overview) или [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Список назначений ролей для пользователя

Чтобы вывести список назначений ролей для конкретного пользователя, используйте команду [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list).

```azurecli-interactive
az role assignment list --assignee <assignee>
```

По умолчанию будут отображаться только назначения ролей для текущей подписки. Чтобы просмотреть назначения ролей для текущей подписки и ниже, добавьте `--all` параметр. Чтобы просмотреть наследуемые назначения ролей, `--include-inherited` добавьте параметр.

В следующем примере перечисляются назначения ролей, назначенные непосредственно пользователю *патлонг\@contoso.com* :

```azurecli-interactive
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

## <a name="list-role-assignments-for-a-resource-group"></a>Вывод списка назначений ролей для группы ресурсов

Чтобы получить список назначений ролей, существующих в области группы ресурсов, используйте команду [AZ Role назначений](/cli/azure/role/assignment#az-role-assignment-list).

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

В следующем примере перечислены назначения ролей для группы ресурсов *Pharma-Sales* .

```azurecli-interactive
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Получение списка назначений ролей для подписки

Чтобы получить список всех назначений ролей в области действия подписки, используйте команду [AZ Role назначений](/cli/azure/role/assignment#az-role-assignment-list). Чтобы получить идентификатор подписки, его можно найти в колонке **подписки** в портал Azure или использовать команду [AZ Account List](/cli/azure/account#az-account-list).

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

Пример

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Список назначений ролей для группы управления

Чтобы получить список всех назначений ролей в области группы управления, используйте команду [AZ Role назначений](/cli/azure/role/assignment#az-role-assignment-list). Чтобы получить идентификатор группы управления, его можно найти в колонке " **группы управления** " в портал Azure или можно использовать команду [AZ Account Management-Group List](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Пример

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Вывод списка назначений ролей для управляемого удостоверения

1. Возвращает идентификатор объекта назначенного системой или управляемого пользователем удостоверения.

    Чтобы получить идентификатор объекта для управляемого удостоверения, назначенного пользователем, можно использовать команду [AZ AD SP List](/cli/azure/ad/sp#az-ad-sp-list) или [AZ Identity List](/cli/azure/identity#az-identity-list).

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    Для получения идентификатора объекта управляемого удостоверения, назначенного системой, можно использовать команду [AZ AD SP List](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. Чтобы получить список назначений ролей, используйте команду [AZ Role назначений](/cli/azure/role/assignment#az-role-assignment-list).

    По умолчанию будут отображаться только назначения ролей для текущей подписки. Чтобы просмотреть назначения ролей для текущей подписки и ниже, добавьте `--all` параметр. Чтобы просмотреть наследуемые назначения ролей, `--include-inherited` добавьте параметр.

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>Дальнейшие действия

- [Добавление или удаление назначений ролей Azure с помощью Azure CLI](role-assignments-cli.md)
