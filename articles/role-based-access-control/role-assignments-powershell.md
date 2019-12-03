---
title: Добавление и удаление назначений ролей с помощью Azure RBAC и Azure PowerShell
description: Узнайте, как предоставить доступ к ресурсам Azure для пользователей, групп, субъектов-служб или управляемых удостоверений с помощью управления доступом на основе ролей (RBAC) Azure и Azure PowerShell.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9c16ec978a0689059c60903182337936960c7a4b
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707847"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-powershell"></a>Добавление и удаление назначений ролей с помощью Azure RBAC и Azure PowerShell

в [!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] этой статье описывается назначение ролей с помощью Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Технические условия

Для добавления или удаления назначений ролей необходимо иметь следующее:

- `Microsoft.Authorization/roleAssignments/write` и `Microsoft.Authorization/roleAssignments/delete`, такие как [Администратор доступа пользователей](built-in-roles.md#user-access-administrator) или [Владелец](built-in-roles.md#owner).
- [PowerShell в Azure Cloud Shell](/azure/cloud-shell/overview) или [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Получение идентификаторов объектов

Для добавления или удаления назначений ролей может потребоваться указать уникальный идентификатор объекта. Идентификатор имеет формат: `11111111-1111-1111-1111-111111111111`. Идентификатор можно получить с помощью портал Azure или Azure PowerShell.

### <a name="user"></a>Пользователь

Чтобы получить идентификатор объекта для пользователя Azure AD, можно использовать [Get-азадусер](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Группа

Чтобы получить идентификатор объекта для группы Azure AD, можно использовать [Get-азадграуп](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Приложение

Чтобы получить идентификатор объекта для субъекта-службы Azure AD (удостоверения, используемого приложением), можно использовать [Get-азадсервицепринЦипал](/powershell/module/az.resources/get-azadserviceprincipal). Для субъекта-службы используйте идентификатор объекта, а **не** идентификатор приложения.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Добавление назначения роли

В RBAC для предоставления доступа необходимо добавить назначение роли.

### <a name="user-at-a-resource-group-scope"></a>Пользователь в области группы ресурсов

Чтобы добавить назначение роли для пользователя в области группы ресурсов, используйте [New-азролеассигнмент](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="using-the-unique-role-id"></a>Использование уникального идентификатора роли

Существует несколько случаев, когда имя роли может измениться, например:

- Вы используете собственную пользовательскую роль и решили изменить ее имя.
- Вы используете роль предварительного просмотра с именем **(Предварительная версия)** . При освобождении роли роль переименовывается.

> [!IMPORTANT]
> Предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Даже если роль переименована, идентификатор роли не изменяется. Если вы используете скрипты или автоматизацию для создания назначений ролей, рекомендуется использовать уникальный идентификатор роли вместо имени роли. Таким образом, если роль переименована, скорее всего, сценарии будут работать.

Чтобы добавить назначение ролей с помощью уникального идентификатора роли вместо имени роли, используйте [New-азролеассигнмент](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

В следующем примере роль [участника виртуальной машины](built-in-roles.md#virtual-machine-contributor) назначается *(Alain\@example.com* User в области группы ресурсов *Pharma-Sales* . Чтобы получить уникальный идентификатор роли, можно использовать [Get-азроледефинитион](/powershell/module/az.resources/get-azroledefinition) или ознакомиться со [встроенными ролями для ресурсов Azure](built-in-roles.md).

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="group-at-a-resource-scope"></a>Группировать в области действия ресурса

Чтобы добавить назначение роли для группы в области действия ресурса, используйте [New-азролеассигнмент](/powershell/module/az.resources/new-azroleassignment). Сведения о том, как получить идентификатор объекта группы, см. в разделе [получение идентификаторов объектов](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="application-at-a-subscription-scope"></a>Приложение в области действия подписки

Чтобы добавить назначение роли для приложения в области действия подписки, используйте [New-азролеассигнмент](/powershell/module/az.resources/new-azroleassignment). Сведения о том, как получить идентификатор объекта приложения, см. в разделе [получение идентификаторов объектов](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="user-at-a-management-group-scope"></a>Пользователь в области группы управления

Чтобы добавить назначение роли для пользователя в области группы управления, используйте [New-азролеассигнмент](/powershell/module/az.resources/new-azroleassignment). Чтобы получить идентификатор группы управления, его можно найти в колонке " **группы управления** " в портал Azure или можно использовать [Get-азманажементграуп](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>Удаление назначения ролей

Для удаления доступа в RBAC нужно удалить назначение ролей с помощью командлета [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

В следующем примере удаляется назначение роли *участника виртуальной машины* из пользователя *(Alain\@example.com* в группе ресурсов *Pharma-Sales* .

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

В следующем примере удаляется роль < role_name > из < object_id > в области действия подписки.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

В следующем примере удаляется роль < role_name > из < object_id > в области группы управления.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

При появление сообщения об ошибке "предоставленные сведения не сопоставлены с назначением ролей" убедитесь, что также заданы параметры `-Scope` или `-ResourceGroupName`. Дополнительные сведения см. в статье [Устранение неполадок RBAC для ресурсов Azure](troubleshooting.md#role-assignments-with-unknown-security-principal).

## <a name="next-steps"></a>Дальнейшие действия

- [Вывод списка назначений ролей с помощью Azure RBAC и Azure PowerShell](role-assignments-list-powershell.md)
- [Руководство. Предоставление группе доступа к ресурсам Azure с помощью RBAC и Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Руководство. Создание настраиваемой роли для ресурсов Azure с помощью Azure PowerShell](tutorial-custom-role-powershell.md)
- [Управление ресурсами с помощью Azure PowerShell](../azure-resource-manager/manage-resources-powershell.md)
