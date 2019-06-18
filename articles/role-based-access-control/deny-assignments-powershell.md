---
title: Список запретить назначения для ресурсов Azure с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как получить список пользователей, групп, субъектов-служб и управляемых удостоверений, которые было отказано в доступе к действиям конкретный ресурс Azure в определенной области, с помощью Azure PowerShell.
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
ms.openlocfilehash: c1ea26fdb4d60262f89ea6ab0f87220a08c01e68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110489"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Список запретить назначения для ресурсов Azure с помощью Azure PowerShell

[Запрещающие назначения](deny-assignments.md) блокируют выполнение определенных действий ресурсов Azure пользователями, даже если назначение роли предоставляет им доступ. В этой статье описывается, как получить список запрещать сбое с помощью Azure PowerShell.

> [!NOTE]
> Невозможно непосредственно создать свои собственные запретить назначения. Сведения о том, как запретить назначения создаются, см. в разделе [запретить назначения](deny-assignments.md).

## <a name="prerequisites"></a>Технические условия

Чтобы получить сведения о назначении deny, необходимо иметь:

- `Microsoft.Authorization/denyAssignments/read` разрешение, которое включается в большинстве [встроенных ролей ресурсов Azure](built-in-roles.md)
- [PowerShell в Azure Cloud Shell](/azure/cloud-shell/overview) или [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Список запретить назначения

### <a name="list-all-deny-assignments"></a>Список всех запретить назначения

Чтобы получить список, все запрещающие назначения для текущей подписки, используйте [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

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

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Список запретить назначений в пределах группы ресурсов

Чтобы получить список, все запрещающие назначений в пределах группы ресурсов, используйте [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

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

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Список запретить назначения в области подписки

Чтобы получить список, все запрещающие назначения в области подписки, используйте [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). Чтобы получить идентификатор подписки, его можно найти в **подписок** колонки на портале Azure, или вы можете использовать [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Дальнейшие действия

- [Запрет назначений для ресурсов Azure](deny-assignments.md)
- [Список запретить назначения для ресурсов Azure с помощью портала Azure](deny-assignments-portal.md)
- [Вывод списка запретов назначений для ресурсов Azure с помощью REST API](deny-assignments-rest.md)