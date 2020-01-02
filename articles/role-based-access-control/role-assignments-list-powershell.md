---
title: Вывод списка назначений ролей с помощью Azure RBAC и Azure PowerShell
description: Узнайте, как определить, к каким ресурсам пользователи, группы, субъекты-службы или управляемые удостоверения имеют доступ с помощью управления доступом на основе ролей (RBAC) Azure и Azure PowerShell.
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
ms.openlocfilehash: c30f1246dccbe14445b0f7db8584e37c0f4be6ab
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710416"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-powershell"></a>Вывод списка назначений ролей с помощью Azure RBAC и Azure PowerShell

в [!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] этой статье описывается, как вывести список назначений ролей с помощью Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Технические условия

- [PowerShell в Azure Cloud Shell](/azure/cloud-shell/overview) или [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-role-assignments-for-the-current-subscription"></a>Вывод списка назначений ролей для текущей подписки

Самый простой способ получить список всех назначений ролей в текущей подписке (включая наследуемые назначения ролей из корневых и групп управления) — использовать [Get-азролеассигнмент](/powershell/module/az.resources/get-azroleassignment) без параметров.

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Получение списка назначений ролей для подписки

Чтобы получить список всех назначений ролей в области действия подписки, используйте [Get-азролеассигнмент](/powershell/module/az.resources/get-azroleassignment). Чтобы получить идентификатор подписки, его можно найти в колонке **подписки** в портал Azure или воспользоваться [Get-азсубскриптион](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>Список назначений ролей для пользователя

Чтобы получить список всех ролей, назначенных соответствующему пользователю, используется команда [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

Чтобы получить список всех ролей, назначенных указанному пользователю и группам, к которым он принадлежит, используется команда [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>Вывод списка назначений ролей для группы ресурсов

Чтобы получить список всех назначений ролей в области группы ресурсов, используйте [Get-азролеассигнмент](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>Список назначений ролей для группы управления

Чтобы получить список всех назначений ролей в области группы управления, используйте [Get-азролеассигнмент](/powershell/module/az.resources/get-azroleassignment). Чтобы получить идентификатор группы управления, его можно найти в колонке " **группы управления** " в портал Azure или можно использовать [Get-азманажементграуп](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Вывод списка назначений ролей классического администратора и соадминистраторов службы

Чтобы получить список назначений ролей для администратора и соадминистраторов классической подписки, используйте командлет [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="next-steps"></a>Дальнейшие действия

- [Добавление и удаление назначений ролей с помощью Azure RBAC и Azure PowerShell](role-assignments-powershell.md)
