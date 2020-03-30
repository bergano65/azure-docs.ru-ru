---
title: Список ролевых заданий с помощью Azure RBAC и Azure CLI
description: Узнайте, как определить, какие ресурсы пользователи, группы, принципы служб или управляемые идентификаторы имеют доступ к использованию управления доступом на основе ролей Azure (RBAC) и Azure CLI.
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
ms.openlocfilehash: 5716e7bb89d017866bd1575256e2d119bb7acbe5
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385067"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Список ролевых заданий с помощью Azure RBAC и Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]В этой статье описывается, как перечислить назначения ролей с помощью Azure CLI.

> [!NOTE]
> Если организация передала функции управления на внешний подряд поставщику услуг, который использует [управление делегированными ресурсами Azure,](../lighthouse/concepts/azure-delegated-resource-management.md)назначения ролей, авторизованные этим поставщиком услуг, не будут отображаться здесь.

## <a name="prerequisites"></a>Предварительные требования

- [Bash в облачной оболочке Azure](/azure/cloud-shell/overview) или [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Список назначений ролей для пользователя

Чтобы вывести список назначений ролей для конкретного пользователя, используйте команду [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list).

```azurecli-interactive
az role assignment list --assignee <assignee>
```

По умолчанию будут отображаться только ролевые назначения для текущей подписки. Для просмотра назначений `--all` ролей для текущей подписки и ниже добавьте параметр. Чтобы просмотреть унаследованные `--include-inherited` назначения ролей, добавьте параметр.

В следующем примере перечислены назначения ролей, которые назначаются непосредственно пользователю *\@patlong contoso.com:*

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

Чтобы перечислить назначения ролей, которые существуют в области группы ресурсов, используйте [список назначений ролей az:](/cli/azure/role/assignment#az-role-assignment-list)

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

В следующем примере перечислены назначения ролей для группы *ресурсов фармацевтических продаж:*

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

Чтобы перечислить все назначения ролей в области подписки, используйте [список назначений ролей az.](/cli/azure/role/assignment#az-role-assignment-list) Чтобы получить идентификатор **подписки,** вы можете найти его на лезвии подписки на портале Azure или вы можете использовать [список учетных записей az.](/cli/azure/account#az-account-list)

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

Пример

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Список назначений ролей для группы управления

Чтобы перечислить все назначения ролей в области группы управления, используйте [список назначений ролей az.](/cli/azure/role/assignment#az-role-assignment-list) Чтобы получить идентификатор группы управления, вы можете найти его на лезвии **групп управления** на портале Azure или использовать [список групп управления учетными записями.](/cli/azure/account/management-group#az-account-management-group-list)

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Пример

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Список назначений ролей для управляемого удостоверения

1. Получите идентификатор объекта, назначенный системой или назначенный пользователем управляемый идентификатор.

    Чтобы получить идентификатор объекта управляемого идентификатора, вы можете использовать [список спа-объявление az](/cli/azure/ad/sp#az-ad-sp-list) или [список идентификационных данных az.](/cli/azure/identity#az-identity-list)

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    Чтобы получить идентификатор объекта управляемого идентификатора, можно использовать [список az ad sp.](/cli/azure/ad/sp#az-ad-sp-list)

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. Чтобы перечислить назначения ролей, используйте [список назначений ролей az.](/cli/azure/role/assignment#az-role-assignment-list)

    По умолчанию будут отображаться только ролевые назначения для текущей подписки. Для просмотра назначений `--all` ролей для текущей подписки и ниже добавьте параметр. Чтобы просмотреть унаследованные `--include-inherited` назначения ролей, добавьте параметр.

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>Дальнейшие действия

- [Добавление или удаление ролевых заданий с помощью Azure RBAC и Azure CLI](role-assignments-cli.md)
