---
title: Вывод списка назначений ролей с помощью Azure RBAC и REST API
description: Узнайте, как определить, к каким ресурсам пользователи, группы, субъекты-службы или управляемые удостоверения имеют доступ с помощью управления доступом на основе ролей (RBAC) Azure и REST API.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e20edcb5e2406c216711a2e0f696ef06e19fe21e
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710403"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Вывод списка назначений ролей с помощью Azure RBAC и REST API

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] этой статье описывается, как вывести список назначений ролей с помощью REST API.

## <a name="list-role-assignments"></a>Список назначений ролей

При использовании RBAC, чтобы узнать, кому предоставлен доступ, вам нужно получить список назначений ролей. Чтобы перечислить назначения ролей, используйте один из REST API [Назначения ролей — список](/rest/api/authorization/roleassignments/list). Чтобы уточнить результаты, укажите область и дополнительный фильтр.

1. Можете начать со следующего запроса:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Внутри URI замените *{scope}* областью, для которой требуется вывести список назначений ролей.

    | Область действия | Тип |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | группа управления; |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | группа ресурсов. |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ресурс |

    В предыдущем примере Microsoft. Web — это поставщик ресурсов, который ссылается на экземпляр службы приложений. Аналогичным образом можно использовать любые другие поставщики ресурсов и указать область. Дополнительные сведения см. в статьях [поставщики ресурсов Azure и типы](../azure-resource-manager/resource-manager-supported-services.md) и поддерживаемые [операции Azure Resource Manager поставщика ресурсов](resource-provider-operations.md).  
     
1. Замените *{filter}* условием, по которому требуется отфильтровать список назначений ролей.

    | Фильтр | Описание |
    | --- | --- |
    | `$filter=atScope()` | Выводит список назначений ролей только для указанной области, не включая назначения ролей в подобластях. |
    | `$filter=principalId%20eq%20'{objectId}'` | Выводит список назначений ролей для указанного пользователя, группы или субъекта-службы. |
    | `$filter=assignedTo('{objectId}')` | Выводит список назначений ролей для указанного пользователя или субъекта-службы. Если пользователь является членом группы, у которой есть назначение ролей, также отображается это назначение роли. Этот фильтр является транзитивным для групп. Это означает, что если пользователь является членом группы, а эта группа является членом другой группы, имеющей назначение ролей, также будет указано назначение роли. Этот фильтр принимает только идентификатор объекта для пользователя или субъекта-службы. Невозможно передать идентификатор объекта для группы. |

## <a name="next-steps"></a>Дальнейшие действия

- [Добавление и удаление назначений ролей с помощью Azure RBAC и REST API](role-assignments-rest.md)
- [Справочник по REST API Azure](/rest/api/azure/)
