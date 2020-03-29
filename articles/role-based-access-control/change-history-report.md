---
title: Просмотр журналов действий для изменений Azure RBAC
description: Просмотр журналов активности для управления доступом на основе ролей Azure (Azure RBAC) изменяются в ресурсы Azure за последние 90 дней.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2024bd14241184338195ed635039bae774da816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161800"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Просмотр журналов действий для изменений Azure RBAC

Иногда требуется информация об изменениях элемента доступа на основе ролей Azure (Azure RBAC), например для аудита или устранения неполадок. Всякий раз, когда кто-то вносит изменения в назначения ролей или определения ролей в ваших подписках, изменения регистрируются в [журнале деятельности Azure.](../azure-monitor/platform/platform-logs-overview.md) Можно просмотреть журналы активности, чтобы просмотреть все изменения Azure RBAC за последние 90 дней.

## <a name="operations-that-are-logged"></a>Операции, которые регистрируются в журнале

Ниже приведены операции, связанные с Azure RBAC, которые регистрируются в журнале активности:

- Создание назначения роли
- Удаление назначения роли
- Создание или изменение определения пользовательской роли
- Удаление определения пользовательской роли

## <a name="azure-portal"></a>Портал Azure

Самый простой способ заключается в просмотре журналов действий на портале Azure. На следующем скриншоте показан пример операций назначения ролей в журнале активности. Она также включает в себя возможность загрузки журналов в качестве файла CSV.

![Снимок экрана: просмотр журналов действий на портале](./media/change-history-report/activity-log-portal.png)

Журнал действий на портале имеет несколько фильтров. Вот фильтры, связанные с Azure RBAC:

| Filter | Значение |
| --------- | --------- |
| Категория событий | <ul><li>Administrative</li></ul> |
| Операция | <ul><li>Создание назначения роли</li><li>Удаление назначения роли</li><li>Создание или изменение определения пользовательской роли</li><li>Удаление определения пользовательской роли</li></ul> |

Для получения дополнительной информации [View activity logs to monitor actions on resources](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)о журналах активности см.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Чтобы просмотреть журналы действий с помощью Azure PowerShell, используйте команду [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog).

Эта команда выводит список всех изменений назначений ролей в подписке за последние семь дней.

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Эта команда выводит список всех изменений определений ролей в подписке за последние семь дней.

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Эта команда выводит список всех изменений назначений ролей и определений ролей в подписке за последние семь дней.

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure CLI

Чтобы просмотреть журналы действий с помощью Azure CLI, используйте команду [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list).

Эта команда перечисляет журналы активности в группе ресурсов от 27 февраля, с нетерпением жду семи дней:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

Эта команда перечисляет журналы действий для поставщика ресурсов авторизации с 27 февраля, с нетерпением жду семи дней:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Журналы Azure Monitor

[Журналы Azure Monitor](../log-analytics/log-analytics-overview.md) — это еще один инструмент, который можно использовать для сбора и анализа изменений Azure RBAC для всех ресурсов Azure. Журналы Azure Monitor имеет следующие преимущества:

- создание сложных запросов и логики;
- интеграция с оповещениями, Power BI и другими средствами;
- хранение данных в течение более длительного срока;
- поддержка перекрестных ссылок с другими журналами, такими как журналы безопасности, журналы виртуальных машин и настраиваемые журналы.

Ниже приведены основные шаги, позволяющие приступить к работе.

1. [Создание рабочего пространства анализа журналов](../azure-monitor/learn/quick-create-workspace.md).

1. [Настройка решения аналитики журнала действий](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) для рабочей области.

1. [Просмотр журналов действий](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Быстрый способ перейти на страницу обзор решения Activity Log Analytics — это нажать на опцию **журналов.**

   ![Опция журналов Azure Monitor на портале](./media/change-history-report/azure-log-analytics-option.png)

1. Дополнительно используйте [аналитику журналов Azure Monitor](../azure-monitor/log-query/get-started-portal.md) для запроса и просмотра журналов. Для получения дополнительной [информации см.](../azure-monitor/log-query/get-started-queries.md)

Ниже приведен запрос, возвращающий новые назначения ролей, упорядоченные по целевому поставщику ресурсов.

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Ниже приведен запрос, возвращающий изменения назначения ролей, отображаемые на схеме.

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Снимок экрана: журналы действий на портале расширенной аналитики](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Дальнейшие действия
* [Просмотр событий в журнале действий](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitor Subscription Activity with the Azure Activity Log](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (Мониторинг действий подписки с помощью журнала действий Azure)
