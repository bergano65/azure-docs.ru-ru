---
title: Создание или обновление пользовательских ролей для ресурсов Azure с помощью Azure CLI Документы Майкрософт
description: Узнайте, как перечислить, создать, обновить или удалить пользовательские роли с помощью элемента управления доступом на основе ролей (RBAC) для ресурсов Azure с помощью Azure CLI.
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 44676f7b92c2bcd30612295840054ab2f0c0cf12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062230"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-azure-cli"></a>Создание или обновление пользовательских ролей для ресурсов Azure с помощью Azure CLI

> [!IMPORTANT]
> Добавление группы `AssignableScopes` управления в настоящее время находится в предварительном просмотре.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Если [встроенные роли для ресурсов Azure](built-in-roles.md) не отвечают конкретным потребностям организации, можно создать собственные пользовательские роли. В этой статье описывается, как перечислять, создавать, обновлять или удалять пользовательские роли с помощью Azure CLI.

Для поэтапного руководства о том, как создать пользовательскую роль, можно ознакомиться на специальном [примере: «Создайте пользовательскую роль для ресурсов Azure с помощью Azure CLI».](tutorial-custom-role-cli.md)

## <a name="prerequisites"></a>Предварительные требования

Для создания пользовательских ролей в Azure требуются:

- разрешения на создание пользовательских ролей, такие как [Владелец](built-in-roles.md#owner) или [Администратор доступа пользователя](built-in-roles.md#user-access-administrator);
- [Azure Cloud Shell](../cloud-shell/overview.md) или [Azure CLI](/cli/azure/install-azure-cli).

## <a name="list-custom-roles"></a>Вывод списка настраиваемых ролей

Чтобы получить список пользовательских ролей, доступных для назначения, используйте команду [az role definition list](/cli/azure/role/definition#az-role-definition-list). В следующих примерах перечислены все пользовательские роли в текущей подписке.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="list-a-custom-role-definition"></a>Перечислите пользовательское определение роли

Чтобы перечислить пользовательское определение роли, используйте [список определения роли az.](/cli/azure/role/definition#az-role-definition-list) Это та же команда, которую вы используете для встроенной роли.

```azurecli
az role definition list --name <role_name>
```

В следующем примере приводится определение роли *оператора виртуальной машины:*

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```Output
[
  {
    "assignableScopes": [
      "/subscriptions/11111111-1111-1111-1111-111111111111"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

В следующем примере перечислены только действия роли *виртуального оператора машины:*

```azurecli
az role definition list --name "Virtual Machine Operator" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Storage/*/read",
  "Microsoft.Network/*/read",
  "Microsoft.Compute/*/read",
  "Microsoft.Compute/virtualMachines/start/action",
  "Microsoft.Compute/virtualMachines/restart/action",
  "Microsoft.Authorization/*/read",
  "Microsoft.ResourceHealth/availabilityStatuses/read",
  "Microsoft.Resources/subscriptions/resourceGroups/read",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Insights/diagnosticSettings/*",
  "Microsoft.Support/*"
]
```

## <a name="create-a-custom-role"></a>Создание настраиваемой роли

Чтобы создать пользовательскую роль, используйте команду [az role definition create](/cli/azure/role/definition#az-role-definition-create). Определением роли может быть описание JSON или путь к файлу, содержащему описание JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

В следующем примере показано создание пользовательской роли *Оператор виртуальной машины*. Пользовательская роль предоставляет доступ ко всем операциям чтения поставщиков ресурсов *Microsoft.Compute*, *Microsoft.Storage* и *Microsoft.Network*, а также доступ для запуска, перезапуска и мониторинга виртуальных машин. Эту настраиваемую роль можно использовать в двух подписках. В этом примере в качестве входных данных используется JSON-файл.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Обновление пользовательской роли

Чтобы обновить пользовательскую роль, сначала используйте команду [az role definition list](/cli/azure/role/definition#az-role-definition-list) для получения определения роли. Затем внесите необходимые изменения в определение роли. И наконец, сохраните обновленное определение роли с помощью команды [az role definition update](/cli/azure/role/definition#az-role-definition-update).

```azurecli
az role definition update --role-definition <role_definition>
```

Следующий пример добавляет *Microsoft.Insights/diagnosticSettings/operation* и `Actions` добавляет `AssignableScopes` группу управления для пользовательской роли *виртуального оператора машин.* Добавление группы `AssignableScopes` управления в настоящее время находится в предварительном просмотре.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Удаление настраиваемой роли

Чтобы удалить пользовательскую роль, используйте команду [az role definition delete](/cli/azure/role/definition#az-role-definition-delete). Чтобы указать роль, которую необходимо удалить, используйте имя или идентификатор роли. Чтобы определить идентификатор роли, используйте команду [az role definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

В следующем примере показано удаление пользовательской роли *Оператор виртуальной машины*.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Дальнейшие действия

- [Учебник: Создайте пользовательскую роль для ресурсов Azure с помощью Azure CLI](tutorial-custom-role-cli.md)
- [Пользовательские роли для ресурсов Azure](custom-roles.md)
- [Операции поставщика ресурсов ресурсов менеджера ресурсов Azure](resource-provider-operations.md)