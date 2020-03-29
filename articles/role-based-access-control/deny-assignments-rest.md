---
title: Список отказов в назначениях для ресурсов Azure с помощью REST API
description: Узнайте, как перечислить задачи отказа в назначении для пользователей, групп и приложений с помощью элемента управления доступом (RBAC) для ресурсов Azure и АИС REST.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0f648405a3d71bf27c64dacbb3fd78f3e9801137
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063017"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Вывод списка запретов назначений для ресурсов Azure с помощью REST API

[Запрет назначений](deny-assignments.md) блокирует пользователям выполнение определенных действий ресурса Azure, даже если назначение ролей предоставляет им доступ. В этой статье описывается, как перечислить отрицание назначений с помощью REST API.

> [!NOTE]
> Вы не можете напрямую создавать собственные назначения отрицания. Для получения [информации](deny-assignments.md)о том, как создаются назначения отрицания, см.

## <a name="prerequisites"></a>Предварительные требования

Чтобы получить информацию об отказе в назначении, необходимо:

- `Microsoft.Authorization/denyAssignments/read`разрешение, которое включено в большинство [встроенных ролей для ресурсов Azure.](built-in-roles.md)

## <a name="list-a-single-deny-assignment"></a>Вывод списка определенного запрета назначения

1. Можете начать со следующего запроса:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Внутри URI замените *{scope}* областью, для которой требуется создать список запретов назначений.

    > [!div class="mx-tableFixed"]
    > | Область | Тип |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Подписка |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Ресурс |

1. Замените *{deny-assignment-id}* идентификатором запрета назначения, которое необходимо получить.

## <a name="list-multiple-deny-assignments"></a>Вывод списка нескольких запретов назначений

1. Начните работу с любого из следующих запросов.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    С использованием необязательных параметров:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Внутри URI замените *{scope}* областью, для которой требуется создать список запретов назначений.

    > [!div class="mx-tableFixed"]
    > | Область | Тип |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Подписка |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Ресурс |

1. Замените *{filter}* условием, по которому требуется отфильтровать список запретов назначений.

    > [!div class="mx-tableFixed"]
    > | Filter | Описание |
    > | --- | --- |
    > | (без фильтра) | Списки все отрицать назначения на, выше, и ниже указанной области. |
    > | `$filter=atScope()` | Списки отрицают назначения только для указанной области и выше. Не включаются запреты назначений во внутренних областях. |
    > | `$filter=assignedTo('{objectId}')` | Списки отрицают назначения для указанного пользователя или директора службы.<br/>Если пользователь является членом группы, у которого есть назначение отрицания, то также указано назначение. Этот фильтр является транзитным для групп, что означает, что если пользователь является членом группы и что группа является членом другой группы, которая имеет назначение отрицания, что отрицание назначения также перечислено.<br/>Этот фильтр принимает идентификатор объекта только для пользователя или директора службы. Вы не можете передать идентификатор объекта для группы. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Списки отрицают назначения для указанного пользователя или основного обслуживания и в указанном объеме. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Списки отрицают назначения с указанным именем. |
    > | `$filter=principalId+eq+'{objectId}'` | Списки отрицают назначения для указанного пользователя, группы или основного обслуживания. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Вывод списка запретов назначений в корневой области (/)

1. Повысьте уровень доступа, как описано в статье [Повышение прав доступа глобального администратора в Azure Active Directory](elevate-access-global-admin.md).

1. Используйте следующий запрос:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Замените *{filter}* условием, по которому требуется отфильтровать список запретов назначений. Необходимо указать фильтр.

    > [!div class="mx-tableFixed"]
    > | Filter | Описание |
    > | --- | --- |
    > | `$filter=atScope()` | Вывод списка запретов назначений только в корневой области. Не включаются запреты назначений во внутренних областях. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Вывод списка всех запретов назначений с указанным названием. |

1. Удалите повышенный уровень доступа.

## <a name="next-steps"></a>Дальнейшие действия

- [Запрет назначений для ресурсов Azure](deny-assignments.md)
- [Повысить доступ для глобального администратора в active-каталоге Azure](elevate-access-global-admin.md)
- [Ссылка на API Azure REST](/rest/api/azure/)
