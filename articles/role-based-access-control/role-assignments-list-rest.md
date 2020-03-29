---
title: Список ролевых заданий с помощью Azure RBAC и API REST
description: Узнайте, как определить, какие ресурсы пользователи, группы, принципы служб или управляемые идентификаторы имеют доступ к использованию управления доступом на основе ролей Azure (RBAC) и API REST.
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
ms.openlocfilehash: a494e7fd4c9fb79faa6a1d8cb2c3c871796ccdc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062161"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Список ролевых заданий с помощью Azure RBAC и API REST

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]В этой статье описывается, как перечислить назначения ролей с помощью REST API.

> [!NOTE]
> Если организация передала функции управления на внешний подряд поставщику услуг, который использует [управление делегированными ресурсами Azure,](../lighthouse/concepts/azure-delegated-resource-management.md)назначения ролей, авторизованные этим поставщиком услуг, не будут отображаться здесь.

## <a name="list-role-assignments"></a>Список назначений ролей

При использовании RBAC, чтобы узнать, кому предоставлен доступ, вам нужно получить список назначений ролей. Чтобы перечислить назначения ролей, используйте один из REST API [Назначения ролей — список](/rest/api/authorization/roleassignments/list). Чтобы уточнить результаты, укажите область и дополнительный фильтр.

1. Можете начать со следующего запроса:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Внутри URI замените *{scope}* областью, для которой требуется вывести список назначений ролей.

    > [!div class="mx-tableFixed"]
    > | Область | Тип |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | группа управления; |
    > | `subscriptions/{subscriptionId1}` | Подписка |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Ресурс |

    В предыдущем примере microsoft.web — это поставщик ресурсов, который относится к экземпляру Службы приложений. Аналогичным образом можно использовать любые другие поставщики ресурсов и указать область охвата. Для получения дополнительной информации смотрите [поставщики и типы ресурсов Ресурсов Azure и](../azure-resource-manager/management/resource-providers-and-types.md) поддерживается [ресурсами, которые поддерживаются ресурсами ресурсного поставщика ресурсов Azure.](resource-provider-operations.md)  
     
1. Замените *{filter}* условием, по которому требуется отфильтровать список назначений ролей.

    > [!div class="mx-tableFixed"]
    > | Filter | Описание |
    > | --- | --- |
    > | `$filter=atScope()` | Перечисляет назначения ролей только для указанной области, не включая назначения ролей в подскопах. |
    > | `$filter=assignedTo('{objectId}')` | Списки назначений ролей для определенного пользователя или директора службы.<br/>Если пользователь является членом группы, выполняя ролевые назначения, это назначение роли также перечислено. Этот фильтр является транзитным для групп, что означает, что если пользователь является членом группы и что группа является членом другой группы, которая имеет назначение ролей, это назначение роли также перечислено.<br/>Этот фильтр принимает идентификатор объекта только для пользователя или директора службы. Вы не можете передать идентификатор объекта для группы. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Перечисляет назначения ролей для указанного пользователя или директора службы и в указанном объеме. |
    > | `$filter=principalId+eq+'{objectId}'` | Списки назначений ролей для определенного пользователя, группы или директора службы. |

## <a name="next-steps"></a>Дальнейшие действия

- [Добавление или удаление ролевых заданий с помощью Azure RBAC и API REST](role-assignments-rest.md)
- [Ссылка на API Azure REST](/rest/api/azure/)
