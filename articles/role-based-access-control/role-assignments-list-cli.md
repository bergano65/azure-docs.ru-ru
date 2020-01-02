---
title: Вывод списка назначений ролей с помощью Azure RBAC и Azure CLI
description: Узнайте, как определить, к каким ресурсам пользователи, группы, субъекты-службы или управляемые удостоверения имеют доступ с помощью управления доступом на основе ролей (RBAC) Azure и Azure CLI.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1a58d2170ec1107222f0e37e432063af23743e42
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710442"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Вывод списка назначений ролей с помощью Azure RBAC и Azure CLI

в [!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] этой статье описывается, как вывести список назначений ролей с помощью Azure CLI.

## <a name="prerequisites"></a>Технические условия

- [Bash в Azure Cloud Shell](/azure/cloud-shell/overview) или [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Список назначений ролей для пользователя

Чтобы вывести список назначений ролей для конкретного пользователя, используйте команду [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list).

```azurecli
az role assignment list --assignee <assignee>
```

По умолчанию будут отображаться только прямые назначения, ограниченные по подписке. Для просмотра назначений, ограниченных ресурсом или группой, используйте `--all` и для просмотра наследуемых назначений используйте `--include-inherited`.

В следующем примере перечисляются назначения ролей, назначенные непосредственно пользователю *патлонг\@contoso.com* :

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
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

```azurecli
az role assignment list --resource-group <resource_group>
```

В следующем примере перечислены назначения ролей для группы ресурсов *Pharma-Sales* .

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
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

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Список назначений ролей для группы управления

Чтобы получить список всех назначений ролей в области группы управления, используйте команду [AZ Role назначений](/cli/azure/role/assignment#az-role-assignment-list). Чтобы получить идентификатор группы управления, его можно найти в колонке " **группы управления** " в портал Azure или можно использовать команду [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="next-steps"></a>Дальнейшие действия

- [Добавление и удаление назначений ролей с помощью Azure RBAC и Azure CLI](role-assignments-cli.md)
