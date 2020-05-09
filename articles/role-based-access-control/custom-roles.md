---
title: Пользовательские роли Azure — Azure RBAC
description: Узнайте, как создавать пользовательские роли Azure с помощью управления доступом на основе ролей Azure (Azure RBAC) для точного управления доступом к ресурсам Azure.
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
ms.date: 04/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5030fb50313e1db2173990c55930c22fdf58f559
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734796"
---
# <a name="azure-custom-roles"></a>Пользовательские роли Azure

> [!IMPORTANT]
> Добавление группы управления в сейчас `AssignableScopes` находится на этапе предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Если [встроенные роли Azure](built-in-roles.md) не отвечают конкретным потребностям Организации, можно создать собственные пользовательские роли. Так же, как и встроенные роли, можно назначать пользовательские роли пользователям, группам и субъектам-службам в областях групп управления, подписок и групп ресурсов.

Пользовательские роли могут совместно использоваться подписками, которые доверяют одному каталогу Azure AD. Существует ограничение в **5 000** пользовательских ролей для каталога. (Для Azure для Германии и Azure для Китая с помощью 21Vianet ограничение составляет 2 000 пользовательских ролей.) Пользовательские роли можно создавать с помощью портал Azure, Azure PowerShell, Azure CLI или REST API.

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
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

После создания пользовательская роль отображается на портале Azure с оранжевым значком ресурса.

![Имя пользовательской роли](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Процедура создания пользовательской роли

1. Выбор способа создания пользовательской роли

    Пользовательские роли можно создавать с помощью [портал Azure](custom-roles-portal.md), [Azure PowerShell](custom-roles-powershell.md), [Azure CLI](custom-roles-cli.md)или [REST API](custom-roles-rest.md).

1. Определение требуемых разрешений

    При создании пользовательской роли вам нужно знать, какие операции поставщика ресурсов доступны, чтобы определить разрешения. Чтобы просмотреть список операций, см. [Azure Resource Manager операции с поставщиком ресурсов](resource-provider-operations.md). Вы добавите операции в свойства `Actions` или `NotActions` [определения роли](role-definitions.md). Если у вас есть операции с данными, вы добавите их `DataActions` в `NotDataActions` свойства или.

1. Создание настраиваемой роли

    В общем случае вы начинаете с существующей встроенной роли и видоизменяете ее под свои потребности. После этого можно воспользоваться командой [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) или [az role definition create](/cli/azure/role/definition#az-role-definition-create) для создания настраиваемой роли. Чтобы создать пользовательскую роль, нужно иметь разрешение `Microsoft.Authorization/roleDefinitions/write` для всех объектов `AssignableScopes`, таких как [Владелец](built-in-roles.md#owner) или [Администратор доступа пользователей](built-in-roles.md#user-access-administrator).

1. Проверка пользовательской роли

    Создав пользовательскую роль, нужно проверить правильность ее работы. Если позже потребуется корректировка, вы можете обновить пользовательскую роль.

Пошаговое руководство по созданию настраиваемой роли см. в разделе [учебник. Создание настраиваемой роли Azure с помощью Azure PowerShell](tutorial-custom-role-powershell.md) или [учебника. Создание настраиваемой роли azure с помощью Azure CLI](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Свойства пользовательской роли

Пользовательская роль имеет указанные ниже свойства.

| Свойство | Обязательно | Тип | Описание |
| --- | --- | --- | --- |
| `Name` | Да | Строка | Отображаемое имя пользовательской роли. Хотя определение роли — это группа управления или ресурс уровня подписки, определение роли можно использовать в нескольких подписках, использующих один каталог Azure AD. Это отображаемое имя должно быть уникальным в рамках каталога Azure AD. Может содержать буквы, цифры, пробелы и специальные знаки. Максимальное количество знаков — 128. |
| `Id` | Да | Строка | Уникальный идентификатор настраиваемой роли. Для Azure PowerShell и Azure CLI этот идентификатор формируется автоматически при создании роли. |
| `IsCustom` | Да | Строка | Указывает, является ли эта роль настраиваемой. Для настраиваемых пролей задайте значение `true`. |
| `Description` | Да | Строка | Описание пользовательской роли. Может содержать буквы, цифры, пробелы и специальные знаки. Максимальное количество знаков — 1024. |
| `Actions` | Да | String[] | Массив строк, описывающий операции управления, которые роль разрешает выполнять. Дополнительные сведения см. в разделе [Actions](role-definitions.md#actions). |
| `NotActions` | нет | String[] | Массив строк, указывающий операции управления, которые исключаются из разрешенных `Actions`. Дополнительные сведения см. в разделе [NotActions](role-definitions.md#notactions). |
| `DataActions` | нет | String[] | Массив строк, указывающий операции с данными, которые роль разрешает выполнять с вашими данными внутри этого объекта. Если создать пользовательскую роль с `DataActions`, эту роль нельзя будет назначить в области группы управления. Дополнительные сведения см. в разделе [действия](role-definitions.md#dataactions)с данными. |
| `NotDataActions` | нет | String[] | Массив строк, указывающий операции с данными, которые исключаются из разрешенных `DataActions`. Дополнительные сведения см. в разделе [нотдатаактионс](role-definitions.md#notdataactions). |
| `AssignableScopes` | Да | String[] | Массив строк, который указывает области, в которых пользовательская роль может быть назначена. В `AssignableScopes` пользовательской роли можно определить только одну группу управления. Добавление группы управления в сейчас `AssignableScopes` находится на этапе предварительной версии. Дополнительные сведения см. в разделе [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Кто может создавать, удалять, обновлять или просматривать пользовательскую роль

Как и в случае со встроенными ролями, свойство `AssignableScopes` указывает области, в которых роль может быть назначена. Свойство `AssignableScopes` пользовательской роли также определяет, кто может создавать, удалять, изменять или просматривать пользовательскую роль.

| Задача | Операция | Описание |
| --- | --- | --- |
| Создание или удаление пользовательской роли | `Microsoft.Authorization/ roleDefinitions/write` | Пользователи с разрешением на эту операцию для всех `AssignableScopes` пользовательской роли могут создавать (или удалять) пользовательские роли для использования в этих областях. Например, [владельцы](built-in-roles.md#owner) и [Администраторы доступа пользователей](built-in-roles.md#user-access-administrator) групп управления, подписок и групп ресурсов. |
| Обновление пользовательской роли | `Microsoft.Authorization/ roleDefinitions/write` | Пользователи с разрешением на эту операцию для всех `AssignableScopes` пользовательской роли могут обновлять пользовательские роли в этих областях. Например, [владельцы](built-in-roles.md#owner) и [Администраторы доступа пользователей](built-in-roles.md#user-access-administrator) групп управления, подписок и групп ресурсов. |
| Просмотр пользовательской роли | `Microsoft.Authorization/ roleDefinitions/read` | Пользователи с разрешением на эту операцию в определенной области могут просматривать пользовательские роли, которые доступны для назначения в этой области. Все встроенные роли обеспечивают доступность пользовательских ролей для назначения. |

## <a name="custom-role-limits"></a>Ограничения пользовательской роли

В следующем списке описаны ограничения для пользовательских ролей.

- Каждый каталог может иметь до **5000** пользовательских ролей.
- Azure для Германии и Azure для Китая в Китае могут иметь до 2000 пользовательских ролей для каждого каталога.
- Нельзя задать `AssignableScopes` в качестве корневой области (`"/"`).
- В `AssignableScopes` пользовательской роли можно определить только одну группу управления. Добавление группы управления в сейчас `AssignableScopes` находится на этапе предварительной версии.
- Пользовательские роли с `DataActions` не могут быть назначены в области группы управления.
- Azure Resource Manager не проверяет существование группы управления в назначаемой области определения роли.

Дополнительные сведения о настраиваемых ролях и группах управления см. в статье [организация ресурсов с помощью групп управления Azure](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="next-steps"></a>Дальнейшие действия
- [Создание или обновление пользовательских ролей Azure с помощью портал Azure](custom-roles-portal.md)
- [Общие сведения о определениях ролей Azure](role-definitions.md)
- [Устранение неполадок в Azure RBAC](troubleshooting.md)
