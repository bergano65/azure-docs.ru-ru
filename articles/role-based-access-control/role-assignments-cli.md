---
title: Добавление и удаление назначений ролей с помощью Azure RBAC и Azure CLI
description: Узнайте, как предоставить доступ к ресурсам Azure для пользователей, групп, субъектов-служб или управляемых удостоверений с помощью управления доступом на основе ролей (RBAC) Azure и Azure CLI.
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
ms.openlocfilehash: 0351721283df68fde910ae16b16d567954c3e6fb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707891"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>Добавление и удаление назначений ролей с помощью Azure RBAC и Azure CLI

в [!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] этой статье описывается назначение ролей с помощью Azure CLI.

## <a name="prerequisites"></a>Технические условия

Для добавления или удаления назначений ролей необходимо иметь следующее:

- `Microsoft.Authorization/roleAssignments/write` и `Microsoft.Authorization/roleAssignments/delete`, такие как [Администратор доступа пользователей](built-in-roles.md#user-access-administrator) или [Владелец](built-in-roles.md#owner).
- [Bash в Azure Cloud Shell](/azure/cloud-shell/overview) или [Azure CLI](/cli/azure)

## <a name="get-object-ids"></a>Получение идентификаторов объектов

Для добавления или удаления назначений ролей может потребоваться указать уникальный идентификатор объекта. Идентификатор имеет формат: `11111111-1111-1111-1111-111111111111`. Идентификатор можно получить с помощью портал Azure или Azure CLI.

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

## <a name="add-a-role-assignment"></a>Добавление назначения роли

В RBAC для предоставления доступа необходимо добавить назначение роли.

### <a name="user-at-a-resource-group-scope"></a>Пользователь в области группы ресурсов

Чтобы добавить назначение роли для пользователя в области группы ресурсов, используйте команду [AZ Role назначение Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

В следующем примере роль *участника виртуальной машины* назначается *патлонг\@contoso.com* User в области группы ресурсов *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>Использование уникального идентификатора роли

Существует несколько случаев, когда имя роли может измениться, например:

- Вы используете собственную пользовательскую роль и решили изменить ее имя.
- Вы используете роль предварительного просмотра с именем **(Предварительная версия)** . При освобождении роли роль переименовывается.

> [!IMPORTANT]
> Предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Даже если роль переименована, идентификатор роли не изменяется. Если вы используете скрипты или автоматизацию для создания назначений ролей, рекомендуется использовать уникальный идентификатор роли вместо имени роли. Таким образом, если роль переименована, скорее всего, сценарии будут работать.

Чтобы добавить назначение ролей с помощью уникального идентификатора роли вместо имени роли, используйте команду [AZ Role назначение Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

В следующем примере роль [участника виртуальной машины](built-in-roles.md#virtual-machine-contributor) назначается пользователю *патлонг\@contoso.com* в области группы ресурсов *Pharma-Sales* . Чтобы получить уникальный идентификатор роли, можно использовать команду [AZ Role Definition List](/cli/azure/role/definition#az-role-definition-list) или ознакомиться со [встроенными ролями для ресурсов Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Группа в области действия подписки

Чтобы добавить назначение роли для группы, используйте команду [AZ Role назначение Create](/cli/azure/role/assignment#az-role-assignment-create). Сведения о том, как получить идентификатор объекта группы, см. в разделе [получение идентификаторов объектов](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

В следующем примере роль *читателя* назначается группе рабочей группы *Анна Mack* с идентификатором 22222222-2222-2222-2222-222222222222 в области действия подписки.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Группировать в области действия ресурса

Чтобы добавить назначение роли для группы, используйте команду [AZ Role назначение Create](/cli/azure/role/assignment#az-role-assignment-create). Сведения о том, как получить идентификатор объекта группы, см. в разделе [получение идентификаторов объектов](#get-object-ids).

В следующем примере роль *участника виртуальной машины* назначается группе группы *Анна Mack* с идентификатором 22222222-2222-2222-2222-222222222222 в области действия ресурса для виртуальной сети с именем *Pharma-Sales-Project-Network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Приложение в области группы ресурсов

Чтобы добавить назначение ролей для приложения, используйте команду [AZ Role назначение Create](/cli/azure/role/assignment#az-role-assignment-create). Сведения о том, как получить идентификатор объекта приложения, см. в разделе [получение идентификаторов объектов](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

В следующем примере роль *участника виртуальной машины* назначается приложению с идентификатором объекта 44444444-4444-4444-4444-444444444444 в области группы ресурсов *Pharma-Sales* .

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Пользователь в области действия подписки

Чтобы добавить назначение роли для пользователя в области подписки, используйте команду [AZ Role назначение Create](/cli/azure/role/assignment#az-role-assignment-create). Чтобы получить идентификатор подписки, его можно найти в колонке **подписки** в портал Azure или использовать команду [AZ Account List](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

В следующем примере роль *читателя* назначается пользователю *аннм\@example.com* в области действия подписки.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Пользователь в области группы управления

Чтобы добавить назначение роли для пользователя в области группы управления, используйте команду [AZ Role назначение Create](/cli/azure/role/assignment#az-role-assignment-create). Чтобы получить идентификатор группы управления, его можно найти в колонке " **группы управления** " в портал Azure или можно использовать команду [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

В следующем примере роль *читателя выставления счетов* назначается пользователю *(Alain\@example.com* в области группы управления.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Новый субъект-служба

Если создать новый субъект-службу и сразу же попытаться назначить роль этому субъекту-службе, в некоторых случаях такое назначение роли может завершиться ошибкой. Например, при использовании скрипта для создания нового управляемого удостоверения и последующей попытке назначить роль этому субъекту-службе может произойти сбой назначения роли. Причиной этого сбоя может быть задержка репликации. Субъект-служба создается в одном регионе; Однако назначение роли может происходить в другом регионе, который еще не реплицирует субъект-службу. Для решения этой ситуации необходимо указать тип участника при создании назначения роли.

Чтобы добавить назначение ролей, используйте команду [AZ Role назначение Create](/cli/azure/role/assignment#az-role-assignment-create), укажите значение для `--assignee-object-id`, а затем установите для `--assignee-principal-type` `ServicePrincipal`.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

В следующем примере роль *участника виртуальной машины* назначается управляемому удостоверению *MSI-Test* в области группы ресурсов *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Удаление назначения ролей

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

## <a name="next-steps"></a>Дальнейшие действия

- [Вывод списка назначений ролей с помощью Azure RBAC и Azure CLI](role-assignments-list-cli.md)
- [Управление ресурсами и группами ресурсов Azure с помощью интерфейса командной строки Azure](../azure-resource-manager/cli-azure-resource-manager.md)
