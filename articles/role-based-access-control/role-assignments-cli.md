---
title: Добавление или удаление ролевых заданий с помощью Azure RBAC и Azure CLI
description: Узнайте, как предоставить доступ к ресурсам Azure для пользователей, групп, директоров служб или управляемых идентификаторов с помощью управления доступом на основе ролей Azure (RBAC) и Azure CLI.
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
ms.openlocfilehash: b32df50715d5e7276861e0696df1bd6ceb3f684e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245672"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>Добавление или удаление ролевых заданий с помощью Azure RBAC и Azure CLI

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]В этой статье описывается, как назначить роли с помощью Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

Чтобы добавить или удалить назначения ролей, необходимо:

- `Microsoft.Authorization/roleAssignments/write` и `Microsoft.Authorization/roleAssignments/delete`, такие как [Администратор доступа пользователей](built-in-roles.md#user-access-administrator) или [Владелец](built-in-roles.md#owner).
- [Bash в облачной оболочке Azure](/azure/cloud-shell/overview) или [Azure CLI](/cli/azure)

## <a name="get-object-ids"></a>Получить идоты объектов

Для добавления или удаления назначений ролей может потребоваться указать уникальный идентификатор объекта. Идентификатор `11111111-1111-1111-1111-111111111111`имеет формат: . Идентификатор можно получить с помощью портала Azure или Azure CLI.

### <a name="user"></a>Пользователь

Чтобы получить идентификатор объекта для пользователя Azure AD, можно использовать [объявление пользователя az.](/cli/azure/ad/user#az-ad-user-show)

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Группа

Чтобы получить идентификатор объекта для группы Azure AD, можно использовать [группу ad show az](/cli/azure/ad/group#az-ad-group-show) или [список группы ad az.](/cli/azure/ad/group#az-ad-group-list)

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Приложение

Чтобы получить идентификатор объекта для основного сервиса Azure AD (идентификация, используемая приложением), можно использовать [список az ad sp.](/cli/azure/ad/sp#az-ad-sp-list) Для основного обслуживания используйте идентификатор объекта, а **не** идентификатор приложения.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>Добавление назначения роли

В RBAC, чтобы предоставить доступ, вы добавляете назначение ролей.

### <a name="user-at-a-resource-group-scope"></a>Пользователь в области группы ресурсов

Чтобы добавить назначение ролей для пользователя в области группы ресурсов, используйте [назначение роли az.](/cli/azure/role/assignment#az-role-assignment-create)

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

Следующий пример присваивает роль *вкладчика виртуальной машины* для того чтобы *\@patlong contoso.com* потребителя на сфере группы ресурсов *фарма-продаж:*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>Использование уникального идентификатора ролей

Есть несколько раз, когда имя роли может измениться, например:

- Вы используете свою собственную роль, и вы решили изменить имя.
- Вы используете роль предварительного просмотра, которая **имеет (Предварительный просмотр)** в имени. Когда роль освобождается, роль переименовывается.

> [!IMPORTANT]
> Предварительная версия предоставляется без соглашения об уровне обслуживания, и она не рекомендуется для производственных нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Даже если роль переименована, идентификатор роли не изменяется. Если вы используете скрипты или автоматизацию для создания ролевых заданий, рекомендуется использовать уникальный идентификатор роли вместо имени роли. Поэтому, если роль переименована, ваши скрипты с большей вероятностью будут работать.

Чтобы добавить назначение ролей, используя уникальный идентификатор роли вместо имени роли, используйте [назначение роли az.](/cli/azure/role/assignment#az-role-assignment-create)

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

Следующий пример присваивает роль [вкладчика виртуальной машины](built-in-roles.md#virtual-machine-contributor) пользователю *\@patlong contoso.com* в области группы ресурсов *фармацевтических продаж.* Чтобы получить уникальный идентификатор ролей, можно использовать [список определений ролей az](/cli/azure/role/definition#az-role-definition-list) или увидеть [встроенные роли для ресурсов Azure.](built-in-roles.md)

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Группа по подписке

Чтобы добавить назначение ролей для группы, используйте [назначение роли az.](/cli/azure/role/assignment#az-role-assignment-create) Для получения информации о том, как получить идентификатор объекта группы, [см.](#get-object-ids)

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Следующий пример присваивает роль *читателя* группе *команды Ann Mack* с ID 2222222-2222-2222-2222-22222-22222222 по подписке.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Группа в области ресурсов

Чтобы добавить назначение ролей для группы, используйте [назначение роли az.](/cli/azure/role/assignment#az-role-assignment-create) Для получения информации о том, как получить идентификатор объекта группы, [см.](#get-object-ids)

Следующий пример присваивает роль *virtual Machine Contributor* группе ann Mack *Team* с ID 2222222-2222-2222-2222-22222222 22222222 на ресурсной сфере для виртуальной сети под названием *pharma-sales-project-network.*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Применение в области ресурсной группы

Чтобы добавить назначение ролей для приложения, используйте [назначение роли az.](/cli/azure/role/assignment#az-role-assignment-create) Для получения информации о том, как получить идентификатор объекта приложения, [см.](#get-object-ids)

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

Следующий пример присваивает роль *вкладчика виртуальной машины* приложению с идентификатором объекта 444444-4444-4444-4444-44444444 в области ресурсной группы *фармацевтических продаж.*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Пользователь в сфере подписки

Чтобы добавить назначение ролей для пользователя в области подписки, используйте [назначение роли аз.](/cli/azure/role/assignment#az-role-assignment-create) Чтобы получить идентификатор **подписки,** вы можете найти его на лезвии подписки на портале Azure или вы можете использовать [список учетных записей az.](/cli/azure/account#az-account-list)

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

Следующий пример присваивает роль *читателя* *пользователю\@annm example.com* в области подписки.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Пользователь в области группы управления

Чтобы добавить назначение ролей для пользователя в области группы управления, используйте [назначение роли az.](/cli/azure/role/assignment#az-role-assignment-create) Чтобы получить идентификатор группы управления, вы можете найти его на лезвии **групп управления** на портале Azure или использовать [список групп управления учетными записями.](/cli/azure/account/management-group#az-account-management-group-list)

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Следующий пример присваивает роль *Биллинг-читателя* *\@alain example.com* пользователю в области группы управления.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Новый директор службы

Если вы создаете новый директор службы и сразу же попытаетесь назначить роль директору службы, в некоторых случаях это назначение роли может выйти из строя. Например, если вы используете скрипт для создания нового управляемого идентификатора, а затем пытаетесь назначить роль директору службы, назначение роли может выйти из строя. Причиной сбоя, скорее всего, является задержка репликации. Директор службы создается в одном регионе; однако назначение ролей может произойти в другом регионе, который еще не реплицировал основной службы. Для решения этого сценария следует указать основной тип при создании назначения ролей.

Чтобы добавить назначение роли, используйте [назначение роли az,](/cli/azure/role/assignment#az-role-assignment-create)укажите значение для, `--assignee-object-id`а затем установите. `--assignee-principal-type` `ServicePrincipal`

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Следующий пример присваивает роль *вкладчика виртуальной машины* управляемой *идентичности msi-test* в области группы ресурсов *фармацевтических продаж:*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Удаление назначения ролей

При использовании RBAC, чтобы удалить доступ, нужно удалить назначение роли с помощью команды [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

Следующий пример удаляет назначение роли *вкладчика виртуальной машины* от пользователя *\@patlong contoso.com* на группе ресурсов *фарма-продаж:*

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

Следующий пример удаляет роль *читателя* из *группы Энн Мак команды* с ID 22222222-2222-2222-2222-2222-2222222222 по подписке области. Для получения информации о том, как получить идентификатор объекта группы, [см.](#get-object-ids)

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

Следующий пример удаляет роль *Биллинг-читателя* из *\@alain example.com* пользователя в области группы управления. Чтобы получить идентификатор группы управления, можно использовать [список группы управления учетными записями az.](/cli/azure/account/management-group#az-account-management-group-list)

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Дальнейшие действия

- [Список ролевых заданий с помощью Azure RBAC и Azure CLI](role-assignments-list-cli.md)
- [Управление ресурсами и группами ресурсов Azure с помощью интерфейса командной строки Azure](../azure-resource-manager/cli-azure-resource-manager.md)
