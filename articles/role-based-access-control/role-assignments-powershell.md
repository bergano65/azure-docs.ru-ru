---
title: Добавление или удаление ролевых заданий с помощью RBAC и Azure PowerShell
description: Узнайте, как предоставить доступ к ресурсам Azure для пользователей, групп, директоров служб или управляемых идентификаторов с помощью управления доступом на основе ролей Azure (RBAC) и Azure PowerShell.
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
ms.openlocfilehash: 68a73f622dc69b70870ddc1db16edcf406b63800
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283216"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-powershell"></a>Добавление или удаление ролевых заданий с помощью Azure RBAC и Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]В этой статье описывается, как назначить роли с помощью Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

Чтобы добавить или удалить назначения ролей, необходимо:

- `Microsoft.Authorization/roleAssignments/write` и `Microsoft.Authorization/roleAssignments/delete`, такие как [Администратор доступа пользователей](built-in-roles.md#user-access-administrator) или [Владелец](built-in-roles.md#owner).
- [Мощность Shell в облачной оболочке Azure](/azure/cloud-shell/overview) или [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Получить идоты объектов

Для добавления или удаления назначений ролей может потребоваться указать уникальный идентификатор объекта. Идентификатор `11111111-1111-1111-1111-111111111111`имеет формат: . Идентификатор можно получить с помощью портала Azure или Azure PowerShell.

### <a name="user"></a>Пользователь

Чтобы получить идентификатор объекта для пользователя Azure AD, можно использовать [Get-AzADUser.](/powershell/module/az.resources/get-azaduser)

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Группа

Чтобы получить идентификатор объекта для группы Azure AD, можно использовать [Get-AzADGroup.](/powershell/module/az.resources/get-azadgroup)

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Приложение

Чтобы получить идентификатор объекта для основного сервиса Azure AD (идентификация, используемая приложением), можно использовать [Get-AzADServicePrincipal.](/powershell/module/az.resources/get-azadserviceprincipal) Для основного обслуживания используйте идентификатор объекта, а **не** идентификатор приложения.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Добавление назначения роли

В RBAC, чтобы предоставить доступ, вы добавляете назначение ролей.

### <a name="user-at-a-resource-group-scope"></a>Пользователь в области группы ресурсов

Чтобы добавить назначение ролей для пользователя в области группы ресурсов, используйте [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment)

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

### <a name="using-the-unique-role-id"></a>Использование уникального идентификатора ролей

Есть несколько раз, когда имя роли может измениться, например:

- Вы используете свою собственную роль, и вы решили изменить имя.
- Вы используете роль предварительного просмотра, которая **имеет (Предварительный просмотр)** в имени. Когда роль освобождается, роль переименовывается.

> [!IMPORTANT]
> Предварительная версия предоставляется без соглашения об уровне обслуживания, и она не рекомендуется для производственных нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Даже если роль переименована, идентификатор роли не изменяется. Если вы используете скрипты или автоматизацию для создания ролевых заданий, рекомендуется использовать уникальный идентификатор роли вместо имени роли. Поэтому, если роль переименована, ваши скрипты с большей вероятностью будут работать.

Чтобы добавить ролевой задании, используя уникальный идентификатор роли вместо имени роли, используйте [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment)

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

Следующий пример присваивает роль [вкладчика виртуальной машины](built-in-roles.md#virtual-machine-contributor) *\@alain example.com* пользователем в области *фармацевтических* ресурсов группы продаж. Чтобы получить уникальный идентификатор ролей, можно использовать [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) или увидеть [встроенные роли для ресурсов Azure.](built-in-roles.md)

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

### <a name="group-at-a-resource-scope"></a>Группа в области ресурсов

Чтобы добавить назначение ролей для группы в области ресурсов, используйте [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment) Для получения информации о том, как получить идентификатор объекта группы, [см.](#get-object-ids)

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

### <a name="application-at-a-subscription-scope"></a>Приложение в сфере подписки

Чтобы добавить ролевую уступку для приложения в области подписки, используйте [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment) Для получения информации о том, как получить идентификатор объекта приложения, [см.](#get-object-ids)

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

Чтобы добавить назначение ролей для пользователя в области группы управления, используйте [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment) Чтобы получить идентификатор группы управления, вы можете найти его на лезвии **групп управления** на портале Azure или использовать [Get-AzManagementGroup.](/powershell/module/az.resources/get-azmanagementgroup)

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

Следующий пример удаляет назначение роли *вкладчика виртуальной машины* от *\@алана example.com* пользователя в группе ресурсов *фарм-продаж:*

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

Следующий пример удаляет роль> <role_name из <> object_id в области подписки.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

Следующий пример удаляет роль> <role_name из сферы <object_id> в области управления.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Если вы получили сообщение об ошибке: "Предоставленная информация не отображается на `-Scope` `-ResourceGroupName` назначение ролей", убедитесь, что вы также указать или параметры. Для получения дополнительной информации [см.](troubleshooting.md#role-assignments-with-unknown-security-principal)

## <a name="next-steps"></a>Дальнейшие действия

- [Список ролевых заданий с помощью Azure RBAC и Azure PowerShell](role-assignments-list-powershell.md)
- [Учебник: Предоставить группе доступ к ресурсам Azure с помощью RBAC и Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Учебник: Создайте пользовательскую роль для ресурсов Azure с помощью Azure PowerShell](tutorial-custom-role-powershell.md)
- [Управление ресурсами с помощью Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
