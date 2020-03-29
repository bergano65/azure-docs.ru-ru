---
title: Добавление или удаление ролевых заданий с помощью RBAC и API REST
description: Узнайте, как предоставить доступ к ресурсам Azure для пользователей, групп, директоров служб или управляемых идентификаторов с помощью управления доступом на основе ролей Azure (RBAC) и API REST.
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
ms.openlocfilehash: 9beda6589c03f1b14fc9756af86a9ce0711894c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063009"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Добавление или удаление ролевых заданий с помощью Azure RBAC и API REST

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]В этой статье описывается, как назначить роли с помощью REST API.

## <a name="prerequisites"></a>Предварительные требования

Чтобы добавить или удалить назначения ролей, необходимо:

- `Microsoft.Authorization/roleAssignments/write` и `Microsoft.Authorization/roleAssignments/delete`, такие как [Администратор доступа пользователей](built-in-roles.md#user-access-administrator) или [Владелец](built-in-roles.md#owner).

## <a name="add-a-role-assignment"></a>Добавление назначения роли

В RBAC, чтобы предоставить доступ, вы добавляете назначение ролей. Чтобы добавить назначение ролей, используйте [назначения ролей - Создайте](/rest/api/authorization/roleassignments/create) API REST и укажите принцип безопасности, определение ролей и область охвата. Чтобы вызвать этот API, нужен доступ к операции `Microsoft.Authorization/roleAssignments/write`. Из встроенных ролей эту операцию могут выполнять только [владелец](built-in-roles.md#owner) и [администратор доступа пользователей](built-in-roles.md#user-access-administrator).

1. Используйте REST API [Определения ролей — список](/rest/api/authorization/roledefinitions/list) или изучите раздел [Встроенные роли](built-in-roles.md), чтобы получить идентификатор для определения роли, которое требуется назначить.

1. Используйте средство GUID для создания уникального идентификатора, который будет использоваться для назначения роли. Этот идентификатор имеет следующий формат: `00000000-0000-0000-0000-000000000000`

1. Можете начать со следующего запроса и основного текста:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. Внутри URI замените *{scope}* областью для назначения роли.

    > [!div class="mx-tableFixed"]
    > | Область | Тип |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | группа управления; |
    > | `subscriptions/{subscriptionId1}` | Подписка |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Ресурс |

    В предыдущем примере microsoft.web — это поставщик ресурсов, который относится к экземпляру Службы приложений. Аналогичным образом можно использовать любые другие поставщики ресурсов и указать область охвата. Для получения дополнительной информации смотрите [поставщики и типы ресурсов Ресурсов Azure и](../azure-resource-manager/management/resource-providers-and-types.md) поддерживается [ресурсами, которые поддерживаются ресурсами ресурсного поставщика ресурсов Azure.](resource-provider-operations.md)  

1. Замените *{roleAssignmentName}* идентификатором GUID для назначения роли.

1. В органе запроса замените *«область»* областью для назначения роли.

    > [!div class="mx-tableFixed"]
    > | Область | Тип |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | группа управления; |
    > | `subscriptions/{subscriptionId1}` | Подписка |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Ресурс |

1. Замените *{roleDefinitionId}* идентификатором определения роли.

1. Замените *{principalId}* идентификатором объекта для пользователя, группы или субъекта-службы, которым будет назначена роль.

## <a name="remove-a-role-assignment"></a>Удаление назначения ролей

В RBAC, чтобы удалить доступ, нужно удалить назначение роли. Чтобы удалить назначение роли, используйте REST API [Назначения ролей — удаление](/rest/api/authorization/roleassignments/delete). Чтобы вызвать этот API, нужен доступ к операции `Microsoft.Authorization/roleAssignments/delete`. Из встроенных ролей эту операцию могут выполнять только [владелец](built-in-roles.md#owner) и [администратор доступа пользователей](built-in-roles.md#user-access-administrator).

1. Получите идентификатор (GUID) для назначения роли. Его можно получить при первом создании назначения роли, а также при перечислении назначений ролей.

1. Можете начать со следующего запроса:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Внутри URI замените *{scope}* областью для удаления назначения роли.

    > [!div class="mx-tableFixed"]
    > | Область | Тип |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | группа управления; |
    > | `subscriptions/{subscriptionId1}` | Подписка |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Ресурс |

1. Замените *{roleAssignmentName}* идентификатором GUID для назначения роли.

## <a name="next-steps"></a>Дальнейшие действия

- [Список ролевых заданий с помощью Azure RBAC и API REST](role-assignments-list-rest.md)
- [Развертывание ресурсов с использованием шаблонов и REST API Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
- [Ссылка на API Azure REST](/rest/api/azure/)
- [Создание пользовательских ролей для ресурсов Azure с помощью REST API](custom-roles-rest.md)
