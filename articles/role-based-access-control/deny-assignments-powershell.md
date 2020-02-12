---
title: Список запрещенных назначений для ресурсов Azure с Azure PowerShell
description: Узнайте, как получить список пользователей, групп, субъектов-служб и управляемых удостоверений, которым запрещен доступ к определенным действиям ресурсов Azure в определенных областях с помощью Azure PowerShell.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5ba18b89bd37dbd55350321c503e37ab0590ab87
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137392"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Список запрещенных назначений для ресурсов Azure с помощью Azure PowerShell

[Запрещающие назначения](deny-assignments.md) блокируют выполнение определенных действий ресурсов Azure пользователями, даже если назначение роли предоставляет им доступ. В этой статье описывается, как вывести список запрещенных назначений с помощью Azure PowerShell.

> [!NOTE]
> Вы не можете напрямую создавать собственные назначения Deny. Дополнительные сведения о создании назначений Deny см. в разделе [Deny назначений](deny-assignments.md).

## <a name="prerequisites"></a>предварительные требования

Чтобы получить сведения о назначении Deny, необходимо следующее:

- `Microsoft.Authorization/denyAssignments/read` разрешение, которое входит в большинство [встроенных ролей для ресурсов Azure](built-in-roles.md) .
- [PowerShell в Azure Cloud Shell](/azure/cloud-shell/overview) или [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Отображение списка запрещенных назначений

### <a name="list-all-deny-assignments"></a>Перечислить все назначения Deny

Чтобы получить список всех отклонения назначений для текущей подписки, используйте [Get-азденяссигнмент](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Список запретов назначений в области группы ресурсов

Чтобы получить список всех запрещенных назначений в области группы ресурсов, используйте [Get-азденяссигнмент](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Список запретов назначений в области действия подписки

Чтобы получить список всех запрещенных назначений в области действия подписки, используйте [Get-азденяссигнмент](/powershell/module/az.resources/get-azdenyassignment). Чтобы получить идентификатор подписки, его можно найти в колонке **подписки** в портал Azure или воспользоваться [Get-азсубскриптион](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Дальнейшие действия

- [Запрет назначений для ресурсов Azure](deny-assignments.md)
- [Список запрещенных назначений для ресурсов Azure с помощью портал Azure](deny-assignments-portal.md)
- [Вывод списка запретов назначений для ресурсов Azure с помощью REST API](deny-assignments-rest.md)