---
title: Управление доступом к ресурсам Azure с помощью RBAC и Azure CLI | Документация Майкрософт
description: Узнайте, как управлять доступом пользователей, групп и приложений к ресурсам Azure с помощью управления доступом на основе ролей (RBAC) и Azure CLI. Сюда также входят сведения о том, как перечислять, предоставлять и удалять права доступа.
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
ms.date: 04/17/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1cc3d3eca4063a8120851a9d3de1a85292eacb11
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60344569"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>Управление доступом к ресурсам Azure с помощью RBAC и Azure CLI

[Управление доступом на основе ролей (RBAC)](overview.md) — это способ управления доступом к ресурсам Azure. Из этой статьи вы узнаете, как управлять доступом пользователей, групп и приложений, используя RBAC и Azure CLI.

## <a name="prerequisites"></a>Технические условия

Для управления доступом необходим один из следующих инструментов:

* [Bash в Azure Cloud Shell](/azure/cloud-shell/overview);
* [Интерфейс командной строки Azure](/cli/azure)

## <a name="list-roles"></a>Вывод списка ролей

Чтобы получить список всех доступных определений роли, используйте команду [az role definition list](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

В следующем примере показан список имен доступных определений роли вместе с описанием.

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

В следующем примере перечислены все определения встроенной роли.

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

## <a name="list-a-role-definition"></a>Получение списка определений роли

Чтобы получить список определений ролей, используйте [списка определений роли az](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

В следующем примере показано определение роли *Участник*.

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-actions-of-a-role"></a>Вывод списка действий роли

В следующем примере перечисляются только *действия* и *notActions* из *участник* роли:

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

В следующем примере перечисляются только действия *участник виртуальных машин* роли:

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>Вывод списка доступа

При использовании RBAC, чтобы узнать, кому предоставлен доступ, вам нужно получить список назначений ролей.

### <a name="list-role-assignments-for-a-user"></a>Список назначений ролей для пользователя

Чтобы вывести список назначений ролей для конкретного пользователя, используйте команду [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list).

```azurecli
az role assignment list --assignee <assignee>
```

По умолчанию отображаются только назначения в пределах подписки. Чтобы просмотреть назначения в пределах ресурсов или группы, используйте `--all`.

В следующем примере перечисляются назначения ролей, назначенные непосредственно *patlong\@contoso.com* пользователя:

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

### <a name="list-role-assignments-for-a-resource-group"></a>Вывод списка назначений ролей для группы ресурсов

Чтобы вывести список назначений ролей, имеющихся в группе ресурсов, используйте команду [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list).

```azurecli
az role assignment list --resource-group <resource_group>
```

В следующем примере перечисляются назначения ролей для *pharma-sales* группы ресурсов:

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="grant-access"></a>Предоставление доступа

При использовании RBAC, чтобы предоставить доступ, нужно создать назначение ролей.

### <a name="create-a-role-assignment-for-a-user"></a>Создание назначения ролей для пользователя

Для создания назначения ролей для пользователя в пределах группы ресурсов используйте команду [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

В следующем примере назначается *участник виртуальных машин* роль *patlong\@contoso.com* пользователь на сайте *pharma-sales* области действия группы ресурсов:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Создать назначение роли, используя уникальный идентификатор

Существует несколько раз, когда имя роли, может изменяться, например:

- Вы используете собственную настраиваемую роль, и вы хотите изменить имя.
- При использовании предварительной версии роли, обладающей **(Предварительная версия)** в имени. При отпускании роль, роль будет переименован.

> [!IMPORTANT]
> Предварительная версия предоставляется без соглашения об уровне обслуживания, и не рекомендуется для производственных рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Даже если роль переименовывается, идентификатор роли остается неизменным. Если вы используете сценарии или автоматизации для создания назначений ролей, рекомендуется использовать уникальный идентификатор вместо имени роли. Таким образом Если роль переименовывается, сценарии чаще для работы.

Чтобы создать назначение ролей, используя уникальный идентификатор вместо имени роли, используйте [Создание назначений ролей az](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

В следующем примере назначается [участник виртуальных машин](built-in-roles.md#virtual-machine-contributor) роль *patlong\@contoso.com* пользователь на сайте *pharma-sales* области действия группы ресурсов. Чтобы получить уникальный идентификатор, можно использовать [списка определений роли az](/cli/azure/role/definition#az-role-definition-list) или см. в разделе [встроенных ролей ресурсов Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>Создание назначения ролей для группы

Для создания назначения ролей для группы используйте команду [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

В следующем примере роль *Читатель* присваивается группе *Ann Mack Team* с идентификатором 22222222-2222-2222-2222-222222222222 в пределах подписки. Чтобы получить идентификатор группы, можно использовать команду [az ad group list](/cli/azure/ad/group#az-ad-group-list) или [az ad group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

В следующем примере роль *Участник виртуальной машины* присваивается группе *Ann Mack Team* с идентификатором 22222222-2222-2222-2222-222222222222 в пределах ресурса для виртуальной сети с именем *pharma-sales-project-network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Создание назначения ролей для приложения

Чтобы создать роль для приложения, используйте команду [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

В следующем примере назначается *участник виртуальных машин* роли для приложения с Идентификатором объекта 44444444-4444-4444-4444-444444444444 *pharma-sales* области действия группы ресурсов. Чтобы получить идентификатор объекта приложения, можно использовать команду [az ad app list](/cli/azure/ad/app#az-ad-app-list) или [az ad app show](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

## <a name="remove-access"></a>Запрет доступа

При использовании RBAC, чтобы удалить доступ, нужно удалить назначение роли с помощью команды [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

В следующем примере удаляется *участник виртуальных машин* назначение роли *patlong\@contoso.com* пользователя на *pharma-sales* Группа ресурсов:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

В следующем примере роль *Читатель* удаляется для группы *Ann Mack Team* с идентификатором 22222222-2222-2222-2222-222222222222 в пределах подписки. Чтобы получить идентификатор группы, можно использовать команду [az ad group list](/cli/azure/ad/group#az-ad-group-list) или [az ad group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="next-steps"></a>Дальнейшие действия

- [Учебник. Создание пользовательских ролей для ресурсов Azure с помощью Azure CLI](tutorial-custom-role-cli.md)
- [Управление ресурсами и группами ресурсов Azure с помощью интерфейса командной строки Azure](../azure-resource-manager/cli-azure-resource-manager.md)
