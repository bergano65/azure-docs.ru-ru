---
title: Запрет назначений для ресурсов Azure | Документация Майкрософт
description: Сведения о запрете назначений для ресурсов Azure при управлении доступом на основе ролей (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 2c663b587d2e9ee278fc774c2841899b060ccbcf
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479367"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Запрет назначений для ресурсов Azure

*Запрет назначений*, как и назначение ролей, привязывает набор запрещающих действий к пользователю, группе или субъекту-службе в определенной области с целью отказа в доступе. Запрещающие назначения блокируют выполнение определенных действий ресурсов Azure пользователями, даже если назначение роли предоставляет им доступ.

В этой статье описывается, как определять запреты назначений.

## <a name="how-deny-assignments-are-created"></a>How deny assignments are created

Deny assignments are created and managed by Azure to protect resources. Azure Blueprints and Azure managed apps use deny assignments to protect system-managed resources. Azure Blueprints and Azure managed apps are the only way that deny assignments can be created. You can't directly create your own deny assignments.  Дополнительные сведения см. в статье о [защите новых ресурсов с помощью блокировок ресурсов Azure Blueprints](../governance/blueprints/tutorials/protect-new-resources.md).

> [!NOTE]
> You can't directly create your own deny assignments.

## <a name="compare-role-assignments-and-deny-assignments"></a>Compare role assignments and deny assignments

Deny assignments follow a similar pattern as role assignments, but also have some differences.

| Возможность | Назначение роли | Deny assignment |
| --- | --- | --- |
| Предоставление доступа | :heavy_check_mark: |  |
| Запрет доступа |  | :heavy_check_mark: |
| Can be directly created | :heavy_check_mark: |  |
| Apply at a scope | :heavy_check_mark: | :heavy_check_mark: |
| Exclude principals |  | :heavy_check_mark: |
| Prevent inheritance to child scopes |  | :heavy_check_mark: |
| Apply to [classic subscription administrator](rbac-and-directory-admin-roles.md) assignments |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Свойства запретов назначений

 Запрет назначений имеет следующие свойства:

> [!div class="mx-tableFixed"]
> | Свойство | Обязательно для заполнения | Тип | Описание |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | ДА | Строка | Отображаемое имя запрета назначения. Имена должны быть уникальными в пределах области. |
> | `Description` | Нет | Строка | Описание запрета назначения. |
> | `Permissions.Actions` | Минимум один Actions или один DataActions. | String[] | Массив строк, определяющих операции управления, к которым запрет назначения блокирует доступ. |
> | `Permissions.NotActions` | Нет | String[] | Массив строк, определяющих операции управления, освобожденные от запрета назначения. |
> | `Permissions.DataActions` | Минимум один Actions или один DataActions. | String[] | Массив строк, определяющих операции данных, к которым запрет назначения блокирует доступ. |
> | `Permissions.NotDataActions` | Нет | String[] | Массив строк, определяющих операции данных, исключенные из запрета назначения. |
> | `Scope` | Нет | Строка | Строка, определяющая область применения запрета назначения. |
> | `DoNotApplyToChildScopes` | Нет | Логический | Указывает, применяется ли запрет назначения к дочерним областям. Значение по умолчанию — false. |
> | `Principals[i].Id` | ДА | String[] | Массив идентификаторов объектов для субъектов Azure AD (пользователь, группа, субъект-служба или управляемое удостоверение), к которым применяется запрет назначения. Чтобы охватить все субъекты, оставьте GUID пустым (`00000000-0000-0000-0000-000000000000`). |
> | `Principals[i].Type` | Нет | String[] | An array of object types represented by Principals[i].Id. Set to `SystemDefined` to represent all principals. |
> | `ExcludePrincipals[i].Id` | Нет | String[] | Массив идентификаторов объектов для субъектов Azure AD (пользователь, группа, субъект-служба или управляемое удостоверение), к которым не применяется запрет назначения. |
> | `ExcludePrincipals[i].Type` | Нет | String[] | Массив типов объектов, представленный ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | Нет | Логический | Указывает, был ли запрет назначения создан Azure, а значит, не может быть изменен или удален. В настоящее время все запреты назначений защищаются системой. |

## <a name="the-all-principals-principal"></a>The All Principals principal

To support deny assignments, a system-defined principal named *All Principals* has been introduced. Этот субъект представляет всех пользователей, группы, субъекты-службы и управляемые удостоверения в каталоге Azure AD. Если идентификатор субъекта имеет нулевое значение GUID (`00000000-0000-0000-0000-000000000000`), а тип субъекта — `SystemDefined`, субъект представляет все субъекты. In Azure PowerShell output, All Principals looks like the following:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

All Principals can be combined with `ExcludePrincipals` to deny all principals except some users. All Principals has the following constraints:

- Может использоваться только в `Principals` и не может использоваться в `ExcludePrincipals`.
- Для параметра `Principals[i].Type` нужно задать значение `SystemDefined`.

## <a name="next-steps"></a>Дальнейшие действия

* [List deny assignments for Azure resources using the Azure portal](deny-assignments-portal.md)
* [Общие сведения об определениях ролей для ресурсов Azure](role-definitions.md)
