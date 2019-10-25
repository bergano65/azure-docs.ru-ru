---
title: Пользовательские роли для ресурсов Azure | Документация Майкрософт
description: В этой статье приведены сведения о том, как создать пользовательские роли с управлением доступом на основе ролей (RBAC) для точного управления доступом к ресурсам Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/07/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64161451c0c8b1af7666fcd104d337856e5803c7
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821449"
---
# <a name="custom-roles-for-azure-resources"></a>Пользовательские роли для ресурсов Azure

Если [встроенные роли для ресурсов Azure](built-in-roles.md) не соответствуют потребностям вашей организации, вы можете создать собственные пользовательские роли. Пользовательские роли, так же как и встроенные, можно назначать пользователям, группам и субъектам-службам в рамках подписки, группы ресурсов или области ресурсов.

Пользовательские роли могут совместно использоваться подписками, которые доверяют одному каталогу Azure AD. Существует ограничение в **5 000** пользовательских ролей для каталога. (Для специализированных облаков, таких как Azure для государственных организаций, Azure для Германии и Azure для Китая, это ограничение составляет 2 000 пользовательских ролей.) Пользовательские роли можно создавать с помощью Azure PowerShell, Azure CLI или REST API.

## <a name="custom-role-example"></a>Пример пользовательской роли

Ниже представлена пользовательская роль, отображаемая в формате JSON. Эта пользовательская роль позволяет выполнять мониторинг и перезапуск виртуальных машин.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
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
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/subscriptions/{subscriptionId3}"
  ]
}
```

После создания пользовательская роль отображается на портале Azure с оранжевым значком ресурса.

![Имя пользовательской роли](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Процедура создания пользовательской роли

1. Выбор способа создания пользовательской роли

    Пользовательские роли можно создавать с помощью [Azure PowerShell](custom-roles-powershell.md), [Azure CLI](custom-roles-cli.md)или [REST API](custom-roles-rest.md).

1. Определение требуемых разрешений

    При создании пользовательской роли вам нужно знать, какие операции поставщика ресурсов доступны, чтобы определить разрешения. Чтобы просмотреть список операций, см. [Azure Resource Manager операции с поставщиком ресурсов](resource-provider-operations.md). Вы добавите операции в `Actions` или `NotActions` свойства [определения роли](role-definitions.md). Если у вас есть операции с данными, они будут добавлены в свойства `DataActions` или `NotDataActions`.

1. Создание настраиваемой роли

    В общем случае вы начинаете с существующей встроенной роли и видоизменяете ее под свои потребности. После этого можно воспользоваться командой [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) или [az role definition create](/cli/azure/role/definition#az-role-definition-create) для создания настраиваемой роли. Чтобы создать пользовательскую роль, нужно иметь разрешение `Microsoft.Authorization/roleDefinitions/write` для всех объектов `AssignableScopes`, таких как [Владелец](built-in-roles.md#owner) или [Администратор доступа пользователей](built-in-roles.md#user-access-administrator).

1. Проверка пользовательской роли

    Создав пользовательскую роль, нужно проверить правильность ее работы. Если позже потребуется корректировка, вы можете обновить пользовательскую роль.

Пошаговое руководство по созданию пользовательской роли см. в статье [Руководство. Создание пользовательских ролей с помощью Azure PowerShell](tutorial-custom-role-powershell.md) или [Руководство. Создание пользовательских ролей с помощью Azure CLI](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Свойства пользовательской роли

Пользовательская роль имеет указанные ниже свойства.

| Свойство | Обязательно для заполнения | Тип | Описание |
| --- | --- | --- | --- |
| `Name` | ДА | Строка | Отображаемое имя пользовательской роли. Определение роли — это ресурс уровня подписки, который можно использовать в нескольких подписках, использующих один и тот же каталог Azure AD. Это отображаемое имя должно быть уникальным в рамках каталога Azure AD. Может содержать буквы, цифры, пробелы и специальные знаки. Максимальное количество знаков — 128. |
| `Id` | ДА | Строка | Уникальный идентификатор настраиваемой роли. Для Azure PowerShell и Azure CLI этот идентификатор формируется автоматически при создании роли. |
| `IsCustom` | ДА | Строка | Указывает, является ли эта роль настраиваемой. Для настраиваемых пролей задайте значение `true`. |
| `Description` | ДА | Строка | Описание пользовательской роли. Может содержать буквы, цифры, пробелы и специальные знаки. Максимальное количество знаков — 1024. |
| `Actions` | ДА | String[] | Массив строк, описывающий операции управления, которые роль разрешает выполнять. Дополнительные сведения см. в разделе [Actions](role-definitions.md#actions). |
| `NotActions` | Нет | String[] | Массив строк, указывающий операции управления, которые исключаются из разрешенных `Actions`. Дополнительные сведения см. в разделе [NotActions](role-definitions.md#notactions). |
| `DataActions` | Нет | String[] | Массив строк, указывающий операции с данными, которые роль разрешает выполнять с вашими данными внутри этого объекта. Дополнительные сведения см. в разделе [действия](role-definitions.md#dataactions)с данными. |
| `NotDataActions` | Нет | String[] | Массив строк, указывающий операции с данными, которые исключаются из разрешенных `DataActions`. Дополнительные сведения см. в разделе [нотдатаактионс](role-definitions.md#notdataactions). |
| `AssignableScopes` | ДА | String[] | Массив строк, который указывает области, в которых пользовательская роль может быть назначена. В настоящее время для пользовательских ролей нельзя задать `AssignableScopes` в качестве корневой области (`"/"`) или области группы управления. Дополнительные сведения см. в разделе [AssignableScopes](role-definitions.md#assignablescopes) и [Пользовательское определение и назначение роли RBAC](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Кто может создавать, удалять, обновлять или просматривать пользовательскую роль

Как и в случае со встроенными ролями, свойство `AssignableScopes` указывает области, в которых роль может быть назначена. Свойство `AssignableScopes` пользовательской роли также определяет, кто может создавать, удалять, изменять или просматривать пользовательскую роль.

| Задача | Операция | Описание |
| --- | --- | --- |
| Создание или удаление пользовательской роли | `Microsoft.Authorization/ roleDefinitions/write` | Пользователи с разрешением на эту операцию для всех `AssignableScopes` пользовательской роли могут создавать (или удалять) пользовательские роли для использования в этих областях. Например, [владельцы](built-in-roles.md#owner) или [администраторы доступа пользователей](built-in-roles.md#user-access-administrator) подписок, групп ресурсов и ресурсов. |
| Обновление пользовательской роли | `Microsoft.Authorization/ roleDefinitions/write` | Пользователи с разрешением на эту операцию для всех `AssignableScopes` пользовательской роли могут обновлять пользовательские роли в этих областях. Например, [владельцы](built-in-roles.md#owner) или [администраторы доступа пользователей](built-in-roles.md#user-access-administrator) подписок, групп ресурсов и ресурсов. |
| Просмотр пользовательской роли | `Microsoft.Authorization/ roleDefinitions/read` | Пользователи с разрешением на эту операцию в определенной области могут просматривать пользовательские роли, которые доступны для назначения в этой области. Все встроенные роли обеспечивают доступность пользовательских ролей для назначения. |

## <a name="next-steps"></a>Дальнейшие действия
- [Создание пользовательских ролей для ресурсов Azure с помощью Azure PowerShell](custom-roles-powershell.md).
- [Создание пользовательских ролей для ресурсов Azure с помощью Azure CLI](custom-roles-cli.md).
- [Общие сведения об определениях ролей для ресурсов Azure](role-definitions.md)
- [Устранение неполадок RBAC в Azure](troubleshooting.md).
