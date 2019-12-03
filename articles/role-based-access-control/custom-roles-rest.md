---
title: Создание пользовательских или обновленных ролей для ресурсов Azure с помощью REST API Azure | Документация Майкрософт
description: Узнайте, как перечислять, создавать, обновлять и удалять пользовательские роли с помощью управления доступом на основе ролей (RBAC) для ресурсов Azure, используя REST API.
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
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2e5dcd7593ba0992337396bc7c05cc30351644dc
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702983"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Создание или обновление пользовательских ролей для ресурсов Azure с помощью REST API

Если [встроенные роли для ресурсов Azure](built-in-roles.md) не соответствуют потребностям вашей организации, вы можете создать собственные пользовательские роли. В этой статье описывается, как получить список, создать, обновить или удалить пользовательские роли с помощью REST API.

## <a name="list-custom-roles"></a>Вывод списка настраиваемых ролей

Чтобы получить список всех пользовательских ролей в каталоге, используйте REST API [списка "определения ролей](/rest/api/authorization/roledefinitions/list) ".

1. Можете начать со следующего запроса:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Замените *{Filter}* типом роли.

    | Фильтр | Описание |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Фильтр, основанный на типе Кустомроле |

## <a name="list-custom-roles-at-a-scope"></a>Вывод списка пользовательских ролей в области

Чтобы получить список пользовательских ролей в области, используйте REST API [списка "определения ролей](/rest/api/authorization/roledefinitions/list) ".

1. Можете начать со следующего запроса:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. В URI замените *{scope}* на область, для которой нужно получить список ролей.

    | Область действия | Тип |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscription |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | группа ресурсов. |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ресурс |

1. Замените *{Filter}* типом роли.

    | Фильтр | Описание |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Фильтр, основанный на типе Кустомроле |

## <a name="list-a-custom-role-definition-by-name"></a>Вывод пользовательского определения роли по имени

Чтобы получить сведения о пользовательской роли по ее отображаемому имени, используйте [определения ролей — get](/rest/api/authorization/roledefinitions/get) REST API.

1. Можете начать со следующего запроса:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. В URI замените *{scope}* на область, для которой нужно получить список ролей.

    | Область действия | Тип |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscription |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | группа ресурсов. |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ресурс |

1. Замените *{Filter}* отображаемым именем роли.

    | Фильтр | Описание |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Используйте точное отображаемое имя роли в формате URL-адреса. Например, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Вывод определения пользовательской роли по ИДЕНТИФИКАТОРу

Чтобы получить сведения о пользовательской роли по ее уникальному идентификатору, используйте [определения ролей — get](/rest/api/authorization/roledefinitions/get) REST API.

1. Для получения идентификатора GUID роли используйте REST API [Определения ролей — список](/rest/api/authorization/roledefinitions/list).

1. Можете начать со следующего запроса:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. В URI замените *{scope}* на область, для которой нужно получить список ролей.

    | Область действия | Тип |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscription |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | группа ресурсов. |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ресурс |

1. Замените *{roleDefinitionId}* на идентификатор GUID определения роли.

## <a name="create-a-custom-role"></a>Создание настраиваемой роли

Чтобы создать пользовательскую роль, используйте REST API [Определения ролей — создание или обновление](/rest/api/authorization/roledefinitions/createorupdate). Чтобы вызвать этот API, необходимо войти в систему с помощью пользователя, которому назначена роль, имеющая разрешение `Microsoft.Authorization/roleDefinitions/write` на все `assignableScopes`. Из встроенных ролей это разрешение включает только [владелец](built-in-roles.md#owner) и [администратор доступа пользователей](built-in-roles.md#user-access-administrator) .

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
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. В URI замените *{scope}* на первую область `assignableScopes` пользовательской роли.

    | Область действия | Тип |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscription |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | группа ресурсов. |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ресурс |

1. Замените *{role-definition-id}* на идентификатор GUID пользовательской роли.

1. В тексте запроса в свойстве `assignableScopes` замените *{roleDefinitionId}* на идентификатор GUID.

1. Замените *{subscriptionId}* на идентификатор подписки.

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
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Обновление пользовательской роли

Чтобы обновить пользовательскую роль, используйте REST API [Определения ролей — создание или обновление](/rest/api/authorization/roledefinitions/createorupdate). Чтобы вызвать этот API, необходимо войти в систему с помощью пользователя, которому назначена роль, имеющая разрешение `Microsoft.Authorization/roleDefinitions/write` на все `assignableScopes`. Из встроенных ролей это разрешение включает только [владелец](built-in-roles.md#owner) и [администратор доступа пользователей](built-in-roles.md#user-access-administrator) .

1. Чтобы получить сведения о пользовательской роли, используйте REST API [Определения ролей — список](/rest/api/authorization/roledefinitions/list) или [Определения ролей — получение](/rest/api/authorization/roledefinitions/get). Дополнительные сведения см. в разделе более ранние [пользовательские роли списка](#list-custom-roles) .

1. Можете начать со следующего запроса:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. В URI замените *{scope}* на первую область `assignableScopes` пользовательской роли.

    | Область действия | Тип |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscription |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | группа ресурсов. |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ресурс |

1. Замените *{role-definition-id}* на идентификатор GUID пользовательской роли.

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
          "/subscriptions/{subscriptionId}"
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
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Удаление настраиваемой роли

Чтобы удалить пользовательскую роль, используйте REST API [Определения ролей — удаление](/rest/api/authorization/roledefinitions/delete). Чтобы вызвать этот API, необходимо войти в систему с помощью пользователя, которому назначена роль, имеющая разрешение `Microsoft.Authorization/roleDefinitions/delete` на все `assignableScopes`. Из встроенных ролей это разрешение включает только [владелец](built-in-roles.md#owner) и [администратор доступа пользователей](built-in-roles.md#user-access-administrator) .

1. Чтобы получить идентификатор GUID пользовательской роли, используйте REST API [Определения ролей — список](/rest/api/authorization/roledefinitions/list) или [Определения ролей — получение](/rest/api/authorization/roledefinitions/get). Дополнительные сведения см. в разделе более ранние [пользовательские роли списка](#list-custom-roles) .

1. Можете начать со следующего запроса:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. В URI замените *{scope}* на область, для которой нужно удалить пользовательскую роль.

    | Область действия | Тип |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subscription |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | группа ресурсов. |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ресурс |

1. Замените *{role-definition-id}* на идентификатор GUID пользовательской роли.

## <a name="next-steps"></a>Дальнейшие действия

- [Пользовательские роли для ресурсов Azure](custom-roles.md)
- [Управление доступом к ресурсам Azure с помощью RBAC и REST API](role-assignments-rest.md)
- [Справочник по REST API Azure](/rest/api/azure/)