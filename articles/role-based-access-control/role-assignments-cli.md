---
title: Управление доступом к ресурсам Azure с помощью RBAC и Azure CLI | Документация Майкрософт
description: Узнайте, как управлять доступом пользователей, групп и приложений к ресурсам Azure с помощью управления доступом на основе ролей (RBAC) и Azure CLI. Здесь содержатся инструкции по выводу списка доступа, предоставлению и запрету доступа.
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
ms.date: 11/21/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 795a97f84bebf6c0e7c1692e82df2f7ce11e0bbd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384098"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>Управление доступом к ресурсам Azure с помощью RBAC и Azure CLI

[Управление доступом на основе ролей (RBAC)](overview.md) — это способ управления доступом к ресурсам в Azure. Из этой статьи вы узнаете, как управлять доступом пользователей, групп и приложений, используя RBAC и Azure CLI.

## <a name="prerequisites"></a>предварительным требованиям

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

## <a name="list-a-role-definition"></a>Вывод определения роли

Чтобы получить список определений ролей, используйте команду [AZ Role Definition List](/cli/azure/role/definition#az-role-definition-list):

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

В следующем примере перечисляются только *действия* и *неизменность* роли *участника* :

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

В следующем примере перечисляются только действия роли *участника виртуальной машины* :

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

### <a name="list-role-assignments-at-a-resource-group-scope"></a>Вывод списка назначений ролей в области группы ресурсов

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

### <a name="list-role-assignments-at-a-subscription-scope"></a>Вывод списка назначений ролей в области действия подписки

Чтобы получить список всех назначений ролей в области действия подписки, используйте команду [AZ Role назначений](/cli/azure/role/assignment#az-role-assignment-list). Чтобы получить идентификатор подписки, его можно найти в колонке **подписки** в портал Azure или использовать команду [AZ Account List](/cli/azure/account#az-account-list).

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>Вывод списка назначений ролей в области группы управления

Чтобы получить список всех назначений ролей в области группы управления, используйте команду [AZ Role назначений](/cli/azure/role/assignment#az-role-assignment-list). Чтобы получить идентификатор группы управления, его можно найти в колонке " **группы управления** " в портал Azure или можно использовать команду [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="get-object-ids"></a>Получение идентификаторов объектов

Чтобы вывести список, добавить или удалить назначения ролей, может потребоваться указать уникальный идентификатор объекта. Идентификатор имеет формат: `11111111-1111-1111-1111-111111111111`. Идентификатор можно получить с помощью портал Azure или Azure CLI.

### <a name="user"></a>Пользователь

Чтобы получить идентификатор объекта для пользователя Azure AD, можно использовать команду [AZ AD user демонстрация](/cli/azure/ad/user#az-ad-user-show).

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Группа

Чтобы получить идентификатор объекта для группы Azure AD, можно использовать команду [AZ AD Group показ](/cli/azure/ad/group#az-ad-group-show) или [AZ AD Group List](/cli/azure/ad/group#az-ad-group-list).

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Приложение

Чтобы получить идентификатор объекта для субъекта-службы Azure AD (удостоверения, используемого приложением), можно использовать команду [AZ AD SP List](/cli/azure/ad/sp#az-ad-sp-list). Для субъекта-службы используйте идентификатор объекта, а **не** идентификатор приложения.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="grant-access"></a>Предоставление доступа

Чтобы в RBAC предоставить доступ, нужно создать назначение ролей.

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Создание назначения роли для пользователя в области действия группы ресурсов

Чтобы предоставить пользователю доступ к области группы ресурсов, используйте команду [AZ Role назначение Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

В следующем примере роль *участника виртуальной машины* назначается *патлонг\@contoso.com* User в области группы ресурсов *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Создание назначения роли с помощью уникального идентификатора роли

Существует несколько случаев, когда имя роли может измениться, например:

- Вы используете собственную пользовательскую роль и решили изменить ее имя.
- Вы используете роль предварительного просмотра с именем **(Предварительная версия)** . При освобождении роли роль переименовывается.

> [!IMPORTANT]
> Предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Даже если роль переименована, идентификатор роли не изменяется. Если вы используете скрипты или автоматизацию для создания назначений ролей, рекомендуется использовать уникальный идентификатор роли вместо имени роли. Таким образом, если роль переименована, скорее всего, сценарии будут работать.

Чтобы создать назначение ролей, используя уникальный идентификатор роли вместо имени роли, используйте команду [AZ Role назначение Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

В следующем примере роль [участника виртуальной машины](built-in-roles.md#virtual-machine-contributor) назначается пользователю *патлонг\@contoso.com* в области группы ресурсов *Pharma-Sales* . Чтобы получить уникальный идентификатор роли, можно использовать команду [AZ Role Definition List](/cli/azure/role/definition#az-role-definition-list) или ознакомиться со [встроенными ролями для ресурсов Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>Создание назначения ролей для группы

Чтобы предоставить доступ к группе, используйте команду [AZ Role назначение Create](/cli/azure/role/assignment#az-role-assignment-create). Сведения о том, как получить идентификатор объекта группы, см. в разделе [получение идентификаторов объектов](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

В следующем примере роль *читателя* назначается группе рабочей группы *Анна Mack* с идентификатором 22222222-2222-2222-2222-222222222222 в области действия подписки.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

В следующем примере роль *участника виртуальной машины* назначается группе группы *Анна Mack* с идентификатором 22222222-2222-2222-2222-222222222222 в области действия ресурса для виртуальной сети с именем *Pharma-Sales-Project-Network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application-at-a-resource-group-scope"></a>Создание назначения роли для приложения в области группы ресурсов

Чтобы предоставить доступ к приложению, используйте команду [AZ Role назначение Create](/cli/azure/role/assignment#az-role-assignment-create). Сведения о том, как получить идентификатор объекта приложения, см. в разделе [получение идентификаторов объектов](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

В следующем примере роль *участника виртуальной машины* назначается приложению с идентификатором объекта 44444444-4444-4444-4444-444444444444 в области группы ресурсов *Pharma-Sales* .

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-user-at-a-subscription-scope"></a>Создание назначения роли для пользователя в области подписки

Чтобы предоставить доступ пользователю в области подписки, используйте команду [AZ Role назначение Create](/cli/azure/role/assignment#az-role-assignment-create). Чтобы получить идентификатор подписки, его можно найти в колонке **подписки** в портал Azure или использовать команду [AZ Account List](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

В следующем примере роль *читателя* назначается пользователю *аннм\@example.com* в области действия подписки.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>Создание назначения роли для пользователя в области группы управления

Чтобы предоставить пользователю доступ к области группы управления, используйте команду [AZ Role назначение Create](/cli/azure/role/assignment#az-role-assignment-create). Чтобы получить идентификатор группы управления, его можно найти в колонке " **группы управления** " в портал Azure или можно использовать команду [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

В следующем примере роль *читателя выставления счетов* назначается пользователю *(Alain\@example.com* в области группы управления.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="create-a-role-assignment-for-a-new-service-principal"></a>Создание назначения роли для нового субъекта-службы

Если создать новый субъект-службу и сразу же попытаться назначить роль этому субъекту-службе, в некоторых случаях такое назначение роли может завершиться ошибкой. Например, при использовании скрипта для создания нового управляемого удостоверения и последующей попытке назначить роль этому субъекту-службе может произойти сбой назначения роли. Причиной этого сбоя может быть задержка репликации. Субъект-служба создается в одном регионе; Однако назначение роли может происходить в другом регионе, который еще не реплицирует субъект-службу. Для решения этой ситуации необходимо указать тип участника при создании назначения роли.

Чтобы создать назначение ролей, используйте команду [AZ Role назначение Create](/cli/azure/role/assignment#az-role-assignment-create), укажите значение для параметра `--assignee-object-id`, а затем задайте для `--assignee-principal-type` `ServicePrincipal`.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

В следующем примере роль *участника виртуальной машины* назначается управляемому удостоверению *MSI-Test* в области группы ресурсов *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-access"></a>Запрет доступа

При использовании RBAC, чтобы удалить доступ, нужно удалить назначение роли с помощью команды [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

В следующем примере удаляется назначение роли *участника виртуальной машины* из пользователя *патлонг\@contoso.com* в группе ресурсов *Pharma-Sales* .

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

В следующем примере удаляется роль *читателя* из группы группы *Анна Mack* с идентификатором 22222222-2222-2222-2222-222222222222 в области действия подписки. Сведения о том, как получить идентификатор объекта группы, см. в разделе [получение идентификаторов объектов](#get-object-ids).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

В следующем примере роль *читателя выставления счетов* удаляется из пользователя *(Alain\@example.com* в области группы управления. Чтобы получить идентификатор группы управления, можно использовать команду [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Дополнительная информация

- [Руководство. Создание настраиваемой роли для ресурсов Azure с помощью Azure CLI](tutorial-custom-role-cli.md)
- [Управление ресурсами и группами ресурсов Azure с помощью интерфейса командной строки Azure](../azure-resource-manager/cli-azure-resource-manager.md)
