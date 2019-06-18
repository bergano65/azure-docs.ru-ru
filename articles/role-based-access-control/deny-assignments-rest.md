---
title: Вывод списка запретов назначений для ресурсов Azure с помощью REST API — Azure | Документация Майкрософт
description: Узнайте, как получить список запретить назначения для пользователей, групп и приложений с помощью управления доступом на основе ролей (RBAC) для ресурсов Azure и REST API.
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
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0bc49456f5965846a2de542b4a063bab2d1838bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118288"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Вывод списка запретов назначений для ресурсов Azure с помощью REST API

[Запрещающие назначения](deny-assignments.md) блокируют выполнение определенных действий ресурсов Azure пользователями, даже если назначение роли предоставляет им доступ. В этой статье описывается, как получить список запрещать сбое с помощью REST API.

> [!NOTE]
> Невозможно непосредственно создать свои собственные запретить назначения. Сведения о том, как запретить назначения создаются, см. в разделе [запретить назначения](deny-assignments.md).

## <a name="prerequisites"></a>Технические условия

Чтобы получить сведения о назначении deny, необходимо иметь:

- `Microsoft.Authorization/denyAssignments/read` разрешение, которое включается в большинстве [встроенных ролей ресурсов Azure](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Вывод списка определенного запрета назначения

1. Можете начать со следующего запроса:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Внутри URI замените *{scope}* областью, для которой требуется создать список запретов назначений.

    | `Scope` | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Подписка |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ресурс |

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

    | `Scope` | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Подписка |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ресурс |

1. Замените *{filter}* условием, по которому требуется отфильтровать список запретов назначений.

    | Фильтр | Описание |
    | --- | --- |
    | (без фильтра) | Вывод списка всех запретов назначений, находящихся выше и ниже заданной области. |
    | `$filter=atScope()` | Вывод списка запретов назначений только в указанной области и областях выше нее. Не включаются запреты назначений во внутренних областях. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Вывод списка всех запретов назначений с указанным названием. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Вывод списка запретов назначений в корневой области (/)

1. Повысьте уровень доступа, как описано в статье [Повышение прав доступа глобального администратора в Azure Active Directory](elevate-access-global-admin.md).

1. Используйте следующий запрос:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Замените *{filter}* условием, по которому требуется отфильтровать список запретов назначений. Необходимо указать фильтр.

    | Фильтр | Описание |
    | --- | --- |
    | `$filter=atScope()` | Вывод списка запретов назначений только в корневой области. Не включаются запреты назначений во внутренних областях. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Вывод списка всех запретов назначений с указанным названием. |

1. Удалите повышенный уровень доступа.

## <a name="next-steps"></a>Дальнейшие действия

- [Запрет назначений для ресурсов Azure](deny-assignments.md)
- [Повышение прав доступа глобального администратора в Azure Active Directory](elevate-access-global-admin.md)
- [Справочник по REST API Azure](/rest/api/azure/)
