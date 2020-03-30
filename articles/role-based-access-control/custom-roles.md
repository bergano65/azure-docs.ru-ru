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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9454962e210781559f2fdceb1c36f499c4ae8ff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062174"
---
# <a name="custom-roles-for-azure-resources"></a>Пользовательские роли для ресурсов Azure

> [!IMPORTANT]
> Добавление группы `AssignableScopes` управления в настоящее время находится в предварительном просмотре.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Если [встроенные роли для ресурсов Azure](built-in-roles.md) не отвечают конкретным потребностям организации, можно создать собственные пользовательские роли. Точно так же, как встроенные роли, можно назначить пользовательские роли пользователям, группам и руководителям служб в группах управления, подписке и области групп ресурсов.

Пользовательские роли можно обменивать между подписками, которые доверяют тому же каталогу Azure AD. Существует ограничение **в 5000** пользовательских ролей в каталоге. (Для Azure Germany и Azure China 21Vianet лимит составляет 2000 пользовательских ролей.) Пользовательские роли можно создавать с помощью портала Azure (Preview), Azure PowerShell, Azure CLI или API REST.

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

1. Решите, как вы хотите создать пользовательскую роль

    Вы можете создавать пользовательские роли с помощью [портала Azure](custom-roles-portal.md) (Preview), [Azure PowerShell,](custom-roles-powershell.md) [Azure CLI](custom-roles-cli.md)или [API REST.](custom-roles-rest.md)

1. Определение требуемых разрешений

    При создании пользовательской роли вам нужно знать, какие операции поставщика ресурсов доступны, чтобы определить разрешения. Для просмотра списка операций [Azure Resource Manager resource provider operations](resource-provider-operations.md)см. Вы добавите операции `Actions` к `NotActions` или свойствам [определения роли.](role-definitions.md) Если у вас есть операции данных, `NotDataActions` вы добавите их к или свойствам. `DataActions`

1. Создание настраиваемой роли

    В общем случае вы начинаете с существующей встроенной роли и видоизменяете ее под свои потребности. После этого можно воспользоваться командой [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) или [az role definition create](/cli/azure/role/definition#az-role-definition-create) для создания настраиваемой роли. Чтобы создать пользовательскую роль, нужно иметь разрешение `Microsoft.Authorization/roleDefinitions/write` для всех объектов `AssignableScopes`, таких как [Владелец](built-in-roles.md#owner) или [Администратор доступа пользователей](built-in-roles.md#user-access-administrator).

1. Проверка пользовательской роли

    Создав пользовательскую роль, нужно проверить правильность ее работы. Если позже потребуется корректировка, вы можете обновить пользовательскую роль.

Пошаговое руководство по созданию пользовательской роли см. в статье [Руководство. Создание пользовательских ролей с помощью Azure PowerShell](tutorial-custom-role-powershell.md) или [Руководство. Создание пользовательских ролей с помощью Azure CLI](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Свойства пользовательской роли

Пользовательская роль имеет указанные ниже свойства.

| Свойство | Обязательно | Тип | Описание |
| --- | --- | --- | --- |
| `Name` | Да | Строка | Отображаемое имя пользовательской роли. Хотя определение роли является ресурсом группы управления или уровня подписки, определение роли может быть использовано в нескольких подписках, которые разделяют один и тот же каталог Azure AD. Это отображаемое имя должно быть уникальным в рамках каталога Azure AD. Может содержать буквы, цифры, пробелы и специальные знаки. Максимальное количество знаков — 128. |
| `Id` | Да | Строка | Уникальный идентификатор настраиваемой роли. Для Azure PowerShell и Azure CLI этот идентификатор формируется автоматически при создании роли. |
| `IsCustom` | Да | Строка | Указывает, является ли эта роль настраиваемой. Для настраиваемых пролей задайте значение `true`. |
| `Description` | Да | Строка | Описание пользовательской роли. Может содержать буквы, цифры, пробелы и специальные знаки. Максимальное количество знаков — 1024. |
| `Actions` | Да | String[] | Массив строк, описывающий операции управления, которые роль разрешает выполнять. Дополнительные сведения см. в разделе [Actions](role-definitions.md#actions). |
| `NotActions` | нет | String[] | Массив строк, указывающий операции управления, которые исключаются из разрешенных `Actions`. Дополнительные сведения см. в разделе [NotActions](role-definitions.md#notactions). |
| `DataActions` | нет | String[] | Массив строк, указывающий операции с данными, которые роль разрешает выполнять с вашими данными внутри этого объекта. При создании пользовательской `DataActions`роли с помощью этой роли не может быть назначена область действия группы управления. Для получения дополнительной информации [см.](role-definitions.md#dataactions) |
| `NotDataActions` | нет | String[] | Массив строк, указывающий операции с данными, которые исключаются из разрешенных `DataActions`. Для получения дополнительной информации [см.](role-definitions.md#notdataactions) |
| `AssignableScopes` | Да | String[] | Массив строк, который указывает области, в которых пользовательская роль может быть назначена. Можно определить только одну `AssignableScopes` группу управления в пользовательской роли. Добавление группы `AssignableScopes` управления в настоящее время находится в предварительном просмотре. Дополнительные сведения см. в разделе [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Кто может создавать, удалять, обновлять или просматривать пользовательскую роль

Как и в случае со встроенными ролями, свойство `AssignableScopes` указывает области, в которых роль может быть назначена. Свойство `AssignableScopes` пользовательской роли также определяет, кто может создавать, удалять, изменять или просматривать пользовательскую роль.

| Задача | Операция | Описание |
| --- | --- | --- |
| Создание или удаление пользовательской роли | `Microsoft.Authorization/ roleDefinitions/write` | Пользователи с разрешением на эту операцию для всех `AssignableScopes` пользовательской роли могут создавать (или удалять) пользовательские роли для использования в этих областях. Например, [владельцы](built-in-roles.md#owner) и [администраторы пользовательского доступа](built-in-roles.md#user-access-administrator) групп управления, подписок и групп ресурсов. |
| Обновление пользовательской роли | `Microsoft.Authorization/ roleDefinitions/write` | Пользователи с разрешением на эту операцию для всех `AssignableScopes` пользовательской роли могут обновлять пользовательские роли в этих областях. Например, [владельцы](built-in-roles.md#owner) и [администраторы пользовательского доступа](built-in-roles.md#user-access-administrator) групп управления, подписок и групп ресурсов. |
| Просмотр пользовательской роли | `Microsoft.Authorization/ roleDefinitions/read` | Пользователи с разрешением на эту операцию в определенной области могут просматривать пользовательские роли, которые доступны для назначения в этой области. Все встроенные роли обеспечивают доступность пользовательских ролей для назначения. |

## <a name="custom-role-limits"></a>Пользовательские ограничения роли

В следующем списке описаны ограничения для пользовательских ролей.

- Каждый каталог может иметь до **5000** пользовательских ролей.
- Azure Германия и Azure China 21Vianet могут иметь до 2000 пользовательских ролей для каждого каталога.
- Вы не `AssignableScopes` можете установить`"/"`на корневой области ( ).
- Можно определить только одну `AssignableScopes` группу управления в пользовательской роли. Добавление группы `AssignableScopes` управления в настоящее время находится в предварительном просмотре.
- Пользовательские `DataActions` роли с не могут быть назначены в области группы управления.
- Менеджер ресурсов Azure не проверяет существование группы управления в области определения роли.

Для получения дополнительной информации о пользовательских ролей и групп управления, [перечниОрганизуйте свои ресурсы с группами управления Azure.](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)

## <a name="next-steps"></a>Дальнейшие действия
- [Создание или обновление пользовательских ролей Azure с помощью портала Azure (Предварительный просмотр)](custom-roles-portal.md)
- [Общие сведения об определениях ролей для ресурсов Azure](role-definitions.md)
- [Устранение неполадок RBAC в Azure](troubleshooting.md)
