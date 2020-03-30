---
title: Создание или обновление пользовательских ролей для ресурсов Azure с помощью REST API
description: Узнайте, как перечислить, создать, обновить или удалить пользовательские роли с помощью элемента управления доступом на основе ролей (RBAC) для ресурсов Azure с помощью API REST.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: fda0400310f46da64322654c42af75521746d679
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062187"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Создание или обновление пользовательских ролей для ресурсов Azure с помощью API REST

> [!IMPORTANT]
> Добавление группы `AssignableScopes` управления в настоящее время находится в предварительном просмотре.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Если [встроенные роли для ресурсов Azure](built-in-roles.md) не отвечают конкретным потребностям организации, можно создать собственные пользовательские роли. В этой статье описывается, как перечислять, создавать, обновлять или удалять пользовательские роли с помощью API REST.

## <a name="list-custom-roles"></a>Вывод списка настраиваемых ролей

Чтобы перечислить все пользовательские роли в каталоге, используйте [определения ролей - Список](/rest/api/authorization/roledefinitions/list) REST API.

1. Можете начать со следующего запроса:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Замените *«фильтр»* типом роли.

    > [!div class="mx-tableFixed"]
    > | Filter | Описание |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Фильтр на основе типа CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Список пользовательских ролей в области

Чтобы перечислить пользовательские роли в области, используйте [определения ролей - Список](/rest/api/authorization/roledefinitions/list) REST API.

1. Можете начать со следующего запроса:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. В URI замените *{scope}* на область, для которой нужно получить список ролей.

    > [!div class="mx-tableFixed"]
    > | Область | Тип |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Подписка |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Группа ресурсов |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Ресурс |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | группа управления; |

1. Замените *«фильтр»* типом роли.

    > [!div class="mx-tableFixed"]
    > | Filter | Описание |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Фильтр на основе типа CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Перечислите пользовательское определение роли по имени

Чтобы получить информацию о пользовательской роли по названию дисплея, используйте [Определения ролей - Get](/rest/api/authorization/roledefinitions/get) REST API.

1. Можете начать со следующего запроса:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. В URI замените *{scope}* на область, для которой нужно получить список ролей.

    > [!div class="mx-tableFixed"]
    > | Область | Тип |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Подписка |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Группа ресурсов |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Ресурс |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | группа управления; |

1. Замените *«фильтр»* на имя дисплея для роли.

    > [!div class="mx-tableFixed"]
    > | Filter | Описание |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Используйте точное отображаемое имя роли в формате URL-адреса. Например, `$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Перечислите пользовательское определение роли по идентификатору

Чтобы получить информацию о пользовательской роли по уникальному идентификатору, используйте [Определение ролей - Get](/rest/api/authorization/roledefinitions/get) REST API.

1. Для получения идентификатора GUID роли используйте REST API [Определения ролей — список](/rest/api/authorization/roledefinitions/list).

1. Можете начать со следующего запроса:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. В URI замените *{scope}* на область, для которой нужно получить список ролей.

    > [!div class="mx-tableFixed"]
    > | Область | Тип |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Подписка |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Группа ресурсов |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Ресурс |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | группа управления; |

1. Замените *«roleDefinitionId»* идентификатором GUID определения роли.

## <a name="create-a-custom-role"></a>Создание настраиваемой роли

Чтобы создать пользовательскую роль, используйте REST API [Определения ролей — создание или обновление](/rest/api/authorization/roledefinitions/createorupdate). Чтобы вызвать этот API, необходимо ввести в набор пользователя, `Microsoft.Authorization/roleDefinitions/write` которым назначена `assignableScopes`роль, на которую есть разрешение на все : Из встроенных ролей только [владелец](built-in-roles.md#owner) и [администратор пользовательского доступа](built-in-roles.md#user-access-administrator) включают это разрешение.

1. Просмотрите список доступных [операций поставщиков ресурсов](resource-provider-operations.md) для создания разрешений для пользовательской роли.

1. Используйте средство GUID для создания уникального идентификатора, который будет использоваться в качестве идентификатора пользовательской роли. Этот идентификатор имеет следующий формат: `00000000-0000-0000-0000-000000000000`

1. Можете начать со следующего запроса и основного текста:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. В URI замените *{scope}* на первую область `assignableScopes` пользовательской роли.

    > [!div class="mx-tableFixed"]
    > | Область | Тип |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Подписка |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Группа ресурсов |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | группа управления; |

1. Замените *«roleDefinitionId»* идентификатором графической роли.

1. В органе запроса замените *«roleDefinitionId»* идентификатором GUID.

1. Если `assignableScopes` это подписка или ресурсная группа, замените экземпляры *«subscriptionId»* или *«resourceGroup»* на идентификаторы.

1. Если `assignableScopes` это группа управления, замените экземпляр *«groupId»* идентификатором группы управления. Добавление группы `assignableScopes` управления в настоящее время находится в предварительном просмотре.

1. В свойстве `actions` добавьте операции, которые разрешают выполнение роли.

1. В свойстве `notActions` добавьте операции, которые исключены из допустимых `actions`.

1. В свойствах `roleName` и `description` укажите уникальное имя и описание роли. Дополнительные сведения о свойствах см. в разделе [Пользовательские роли](custom-roles.md).

    Ниже приведен пример текста запроса:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Обновление пользовательской роли

Чтобы обновить пользовательскую роль, используйте REST API [Определения ролей — создание или обновление](/rest/api/authorization/roledefinitions/createorupdate). Чтобы вызвать этот API, необходимо ввести в набор пользователя, `Microsoft.Authorization/roleDefinitions/write` которым назначена `assignableScopes`роль, на которую есть разрешение на все : Из встроенных ролей только [владелец](built-in-roles.md#owner) и [администратор пользовательского доступа](built-in-roles.md#user-access-administrator) включают это разрешение.

1. Чтобы получить сведения о пользовательской роли, используйте REST API [Определения ролей — список](/rest/api/authorization/roledefinitions/list) или [Определения ролей — получение](/rest/api/authorization/roledefinitions/get). Для получения дополнительной информации смотрите более ранний раздел [пользовательских ролей списка.](#list-custom-roles)

1. Можете начать со следующего запроса:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. В URI замените *{scope}* на первую область `assignableScopes` пользовательской роли.

    > [!div class="mx-tableFixed"]
    > | Область | Тип |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Подписка |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Группа ресурсов |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | группа управления; |

1. Замените *«roleDefinitionId»* идентификатором графической роли.

1. На основе сведений о пользовательской роли создайте текст запроса в следующем формате:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Внесите необходимые изменения для пользовательской роли в текст запроса.

    Ниже приведен пример текста запроса с добавленным действием "Параметры диагностики":

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
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
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Удаление настраиваемой роли

Чтобы удалить пользовательскую роль, используйте REST API [Определения ролей — удаление](/rest/api/authorization/roledefinitions/delete). Чтобы вызвать этот API, необходимо ввести в набор пользователя, `Microsoft.Authorization/roleDefinitions/delete` которым назначена `assignableScopes`роль, на которую есть разрешение на все : Из встроенных ролей только [владелец](built-in-roles.md#owner) и [администратор пользовательского доступа](built-in-roles.md#user-access-administrator) включают это разрешение.

1. Чтобы получить идентификатор GUID пользовательской роли, используйте REST API [Определения ролей — список](/rest/api/authorization/roledefinitions/list) или [Определения ролей — получение](/rest/api/authorization/roledefinitions/get). Для получения дополнительной информации смотрите более ранний раздел [пользовательских ролей списка.](#list-custom-roles)

1. Можете начать со следующего запроса:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. В URI замените *{scope}* на область, для которой нужно удалить пользовательскую роль.

    > [!div class="mx-tableFixed"]
    > | Область | Тип |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Подписка |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Группа ресурсов |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | группа управления; |

1. Замените *«roleDefinitionId»* идентификатором графической роли.

## <a name="next-steps"></a>Дальнейшие действия

- [Пользовательские роли для ресурсов Azure](custom-roles.md)
- [Управление доступом к ресурсам Azure с помощью RBAC и REST API](role-assignments-rest.md)
- [Ссылка на API Azure REST](/rest/api/azure/)
