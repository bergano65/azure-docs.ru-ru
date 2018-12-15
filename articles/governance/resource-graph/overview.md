---
title: Обзор Azure Resource Graph
description: Azure Resource Graph — это служба в Azure, которая позволяет выполнять сложные запросы ресурсов в нужном масштабе.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/22/2018
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: ed64f6317fefb9e82dbe14e806499965d926d434
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316205"
---
# <a name="what-is-azure-resource-graph"></a>Что такое Azure Resource Graph

Azure Resource Graph — это служба в Azure, призванная расширить управление ресурсами Azure за счет действенных средств для оценки ресурсов и возможности масштабных запросов ко всем подпискам и группам управления, которые позволят вам эффективно контролировать вашу среду. Такие запросы предоставляют следующие возможности:

- Возможность запрашивать ресурсы, используя сложные фильтры, группирование и сортировку по свойствам ресурсов.
- Возможность многократно оценивать ресурсы с учетом нормативных требований и конвертировать результирующее выражение в определение политики.
- Возможность оценить эффект от применения политик в обширной облачной среде.

В этой документации мы рассмотрим каждую из этих возможностей подробно.

> [!NOTE]
> Azure Resource Graph используется новой функцией поиска "Все ресурсы" на портале Azure. Она предназначена для клиентов, которым нужно управлять масштабными средами.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Как Azure Resource Graph дополняет Azure Resource Manager

Сейчас Azure Resource Manager отправляет данные в кэш с ограниченными ресурсами, который делает доступными несколько полей ресурсов: имя ресурса, идентификатор, тип, группа ресурсов, подписки и расположение. Раньше, чтобы работать с разными свойствами ресурсов, нужно было вызывать каждый отдельный поставщик ресурсов и запрашивать данные свойств для каждого ресурса.

Azure Resource Graph дает вам доступ к свойствам, возвращаемым поставщиками ресурсов, без необходимости вызывать каждый поставщик ресурсов отдельно.

## <a name="the-query-language"></a>Язык запросов

Теперь, когда вы разобрались, что такое Azure Resource Graph, рассмотрим порядок создания запросов.

Важно понимать, что язык запросов Azure Resource Graph похож на [язык запросов Azure Data Explorer](../../data-explorer/data-explorer-overview.md).

Сведения об операциях и функциях, которые можно использовать с Azure Resource Graph, см. в статье [Язык запросов Resource Graph](./concepts/query-language.md). Информацию о ресурсах см. в статье о [просмотре ресурсов](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Разрешения в Azure Resource Graph

Чтобы использовать Resource Graph, необходимо иметь соответствующие права для [управления доступом на основе ролей](../../role-based-access-control/overview.md) (RBAC) как минимум с доступом на чтение ресурсов, которые вы планируете запрашивать. Без минимального разрешения `read` для объекта или группы объектов Azure результаты не будут возвращаться.

## <a name="running-your-first-query"></a>Выполнение первого запроса

Resource Graph поддерживает как Azure CLI, так и Azure PowerShell. Запрос имеет одинаковую структуру для обоих языков. Узнайте, как включить Resource Graph в [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension) и [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Дополнительная информация

- Выполните первый запрос с помощью [Azure CLI](first-query-azurecli.md)
- Выполните первый запрос с помощью [Azure PowerShell](first-query-powershell.md)
- Начните с [начальных запросов](./samples/starter.md)
- Переходите к [расширенным запросам](./samples/advanced.md)