---
title: Управление доступом к ресурсам Azure с помощью RBAC и REST API в Azure | Документация Майкрософт
description: Узнайте, как управлять доступом пользователей, групп и приложений к ресурсам Azure с помощью управления доступом на основе ролей (RBAC) и REST API. Сюда также входят сведения о том, как перечислять, предоставлять и удалять права доступа.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 86ee030e8c97cf3033b9d2d76b8125c64ecf8065
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996471"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>Управление доступом к ресурсам Azure с помощью RBAC и REST API

[Управление доступом на основе ролей (RBAC)](overview.md) — это способ управления доступом к ресурсам Azure. В этой статье описывается, как управлять доступом пользователей, групп и приложений, используя RBAC и REST API.

## <a name="list-access"></a>Вывод списка доступа

При использовании RBAC, чтобы узнать, кому предоставлен доступ, вам нужно получить список назначений ролей. Чтобы перечислить назначения ролей, используйте один из REST API [Назначения ролей — список](/rest/api/authorization/roleassignments/list). Чтобы уточнить результаты, укажите область и дополнительный фильтр.

1. Можете начать со следующего запроса:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Внутри URI замените *{scope}* областью, для которой требуется вывести список назначений ролей.

    | `Scope` | Тип |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Группа управления |
    | `subscriptions/{subscriptionId1}` | Подписка |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

    В предыдущем примере Microsoft. Web — это поставщик ресурсов, который ссылается на экземпляр службы приложений. Аналогичным образом можно использовать любые другие поставщики ресурсов и указать область. Дополнительные сведения см. в статьях [поставщики ресурсов Azure и типы](../azure-resource-manager/resource-manager-supported-services.md) и поддерживаемые [операции Azure Resource Manager поставщика ресурсов](resource-provider-operations.md).  
     
1. Замените *{filter}* условием, по которому требуется отфильтровать список назначений ролей.

    | Filter | Описание |
    | --- | --- |
    | `$filter=atScope()` | Выводит список назначений ролей только для указанной области, не включая назначения ролей в подобластях. |
    | `$filter=principalId%20eq%20'{objectId}'` | Выводит список назначений ролей для указанного пользователя, группы или субъекта-службы. |
    | `$filter=assignedTo('{objectId}')` | Выводит список назначений ролей для указанного пользователя или субъекта-службы. Если пользователь является членом группы, у которой есть назначение ролей, также отображается это назначение роли. Этот фильтр является транзитивным для групп. Это означает, что если пользователь является членом группы, а эта группа является членом другой группы, имеющей назначение ролей, также будет указано назначение роли. Этот фильтр принимает только идентификатор объекта для пользователя или субъекта-службы. Невозможно передать идентификатор объекта для группы. |

## <a name="grant-access"></a>Разрешить доступ

При использовании RBAC, чтобы предоставить доступ, нужно создать назначение ролей. Чтобы создать назначение роли, используйте REST API [Назначения ролей — создание](/rest/api/authorization/roleassignments/create) и укажите субъект безопасности, определение роли и область. Чтобы вызвать этот API, нужен доступ к операции `Microsoft.Authorization/roleAssignments/write`. Из встроенных ролей эту операцию могут выполнять только [владелец](built-in-roles.md#owner) и [администратор доступа пользователей](built-in-roles.md#user-access-administrator).

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

    | `Scope` | Тип |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Группа управления |
    | `subscriptions/{subscriptionId1}` | Подписка |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Замените *{roleAssignmentName}* идентификатором GUID для назначения роли.

1. В тексте запроса замените *{Scope}* областью для назначения роли.

    | `Scope` | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Группа управления |
    | `subscriptions/{subscriptionId1}` | Подписка |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Замените *{roleDefinitionId}* идентификатором определения роли.

1. Замените *{principalId}* идентификатором объекта для пользователя, группы или субъекта-службы, которым будет назначена роль.

## <a name="remove-access"></a>Remove access

В RBAC, чтобы удалить доступ, нужно удалить назначение роли. Чтобы удалить назначение роли, используйте REST API [Назначения ролей — удаление](/rest/api/authorization/roleassignments/delete). Чтобы вызвать этот API, нужен доступ к операции `Microsoft.Authorization/roleAssignments/delete`. Из встроенных ролей эту операцию могут выполнять только [владелец](built-in-roles.md#owner) и [администратор доступа пользователей](built-in-roles.md#user-access-administrator).

1. Получите идентификатор (GUID) для назначения роли. Его можно получить при первом создании назначения роли, а также при перечислении назначений ролей.

1. Можете начать со следующего запроса:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Внутри URI замените *{scope}* областью для удаления назначения роли.

    | `Scope` | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Группа управления |
    | `subscriptions/{subscriptionId1}` | Подписка |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Замените *{roleAssignmentName}* идентификатором GUID для назначения роли.

## <a name="next-steps"></a>Следующие шаги

- [Развертывание ресурсов с использованием шаблонов и REST API Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Справочник по REST API Azure](/rest/api/azure/)
- [Создание пользовательских ролей для ресурсов Azure с помощью REST API](custom-roles-rest.md)
