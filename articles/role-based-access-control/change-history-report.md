---
title: Просмотр журналов действий для изменений Azure RBAC
description: Просмотрите журналы действий для управления доступом на основе ролей Azure (Azure RBAC) на ресурсы Azure за последние 90 дней.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 332d9a9ec28c4309fb1cf1d3e24d3cfd2d7d13d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87321978"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Просмотр журналов действий для изменений Azure RBAC

Иногда требуются сведения об изменениях управления доступом на основе ролей Azure (Azure RBAC), например, в целях аудита или устранения неполадок. Когда кто-то вносит изменения в назначения ролей или определения ролей в ваших подписках, изменения регистрируются в [журнале действий Azure](../azure-monitor/platform/platform-logs-overview.md). Вы можете просмотреть журналы действий, чтобы увидеть все изменения Azure RBAC за последние 90 дней.

## <a name="operations-that-are-logged"></a>Операции, которые регистрируются в журнале

Ниже приведены операции, связанные с Azure RBAC, которые регистрируются в журнале действий.

- Создание назначения роли
- Удаление назначения роли
- Создание или изменение определения пользовательской роли
- Удаление определения пользовательской роли

## <a name="azure-portal"></a>Портал Azure

Самый простой способ заключается в просмотре журналов действий на портале Azure. На следующем снимке экрана показан пример операций назначения ролей в журнале действий. Кроме того, здесь можно загрузить журналы в виде CSV-файла.

![Снимок экрана: просмотр журналов действий на портале](./media/change-history-report/activity-log-portal.png)

Журнал действий на портале имеет несколько фильтров. Ниже приведены фильтры, связанные с Azure RBAC.

| Filter | Значение |
| --------- | --------- |
| Категория событий | <ul><li>Административный</li></ul> |
| Операция | <ul><li>Создание назначения роли</li><li>Удаление назначения роли</li><li>Создание или изменение определения пользовательской роли</li><li>Удаление определения пользовательской роли</li></ul> |

Дополнительные сведения о журналах действий см. [в статье Просмотр журналов действий для отслеживания действий с ресурсами](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

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
                          eventCategory  : Administrative

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

Если для создания назначений ролей используется субъект-служба, свойство caller будет представлять собой идентификатор объекта. Вы можете использовать [Get-азадсервицепринЦипал](/powershell/module/az.resources/get-azadserviceprincipal) для получения сведений о субъекте-службе.

```Example
Caller                  : 44444444-4444-4444-4444-444444444444
EventTimestamp          : 6/4/2020 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: 55555555-5555-5555-5555-555555555555
                          category       : Administrative
```

## <a name="azure-cli"></a>Azure CLI

Чтобы просмотреть журналы действий с помощью Azure CLI, используйте команду [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list).

Эта команда выводит журналы действий в группе ресурсов с 27 февраля по просматривая семь дней:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

Эта команда выводит журналы действий для поставщика ресурсов авторизации с 27 февраля по просматривая семь дней:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Журналы Azure Monitor

[Журналы Azure Monitor](../log-analytics/log-analytics-overview.md) — это еще один инструмент, с помощью которого можно получать и анализировать изменения Azure RBAC для всех ресурсов Azure. Журналы Azure Monitor имеют следующие преимущества:

- создание сложных запросов и логики;
- интеграция с оповещениями, Power BI и другими средствами;
- хранение данных в течение более длительного срока;
- поддержка перекрестных ссылок с другими журналами, такими как журналы безопасности, журналы виртуальных машин и настраиваемые журналы.

Ниже приведены основные шаги, позволяющие приступить к работе.

1. [Создайте рабочую область log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. [Настройка решения аналитики журнала действий](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) для рабочей области.

1. [Просмотр журналов действий](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Чтобы быстро переходить на страницу обзора решения Аналитика журнала действий, щелкните пункт **журналы** .

   ![Параметр журналов Azure Monitor на портале](./media/change-history-report/azure-log-analytics-option.png)

1. При необходимости используйте [Azure Monitor log Analytics](../azure-monitor/log-query/get-started-portal.md) для запроса и просмотра журналов. Дополнительные сведения см. в статье [Приступая к работе с Azure Monitor журнала запросов](../azure-monitor/log-query/get-started-queries.md).

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
