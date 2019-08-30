---
title: Просмотр журналов действий для изменений RBAC в ресурсах Azure | Документация Майкрософт
description: Вы можете просматривать журналы действий на наличие изменений в управлении доступом на основе ролей (RBAC) в ресурсах Azure за последние 90 дней.
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
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5758f480c9216cf71e47509682053b39f0b15bf
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172416"
---
# <a name="view-activity-logs-for-rbac-changes-to-azure-resources"></a>Просмотр журналов действий для изменений RBAC в ресурсах Azure

Иногда могут потребоваться сведения об изменении доступа на основе ролей (RBAC) в ресурсах Azure, например для аудита или устранения неполадок. Каждый раз, когда кто-либо вносит изменения в определения ролей или назначения ролей в ваших подписках, изменения регистрируются в [журнале действий Azure](../azure-monitor/platform/activity-logs-overview.md). Вы можете просматривать журналы действий, чтобы увидеть все изменения в управлении доступом на основе ролей за последние 90 дней.

## <a name="operations-that-are-logged"></a>Операции, которые регистрируются в журнале

Ниже приведены операции, связанные с RBAC, которые регистрируются в журнале действий.

- Создать назначение ролей
- Удалить назначение ролей
- Создание или изменение определения пользовательской роли
- Удалить определение пользовательской роли

## <a name="azure-portal"></a>портала Azure

Самый простой способ заключается в просмотре журналов действий на портале Azure. На следующем снимке экрана показан пример журнала действий, отфильтрованный для отображения операций определения и назначений ролей. Он также содержит ссылку для скачивания журналов в виде CSV-файла.

![Снимок экрана: просмотр журналов действий на портале](./media/change-history-report/activity-log-portal.png)

Журнал действий на портале имеет несколько фильтров. Ниже приведены фильтры, связанные с RBAC.

|Filter  |Значение  |
|---------|---------|
|Категория событий     | <ul><li>Административная</li></ul>         |
|Операция     | <ul><li>Создать назначение ролей</li> <li>Удалить назначение ролей</li> <li>Создание или изменение определения пользовательской роли</li> <li>Удалить определение пользовательской роли</li></ul>      |


Дополнительные сведения о журналах действий см. в статье [Просмотр журналов действий для аудита действий с ресурсами](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

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
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure CLI

Чтобы просмотреть журналы действий с помощью Azure CLI, используйте команду [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list).

Эта команда выводит список журналов действий в группе ресурсов со времени начала.

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2018-04-20T00:00:00Z
```

Эта команда выводит список журналов действий для поставщика ресурсов авторизации со времени начала.

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-monitor-logs"></a>Журналы Azure Monitor

[Журналы Azure Monitor](../log-analytics/log-analytics-overview.md) — это еще один инструмент, с помощью которого можно собираются и анализируются изменения RBAC для всех ресурсов Azure. Журналы Azure Monitor имеют следующие преимущества:

- создание сложных запросов и логики;
- интеграция с оповещениями, Power BI и другими средствами;
- хранение данных в течение более длительного срока;
- поддержка перекрестных ссылок с другими журналами, такими как журналы безопасности, журналы виртуальных машин и настраиваемые журналы.

Ниже приведены основные шаги, позволяющие приступить к работе.

1. [Создание рабочей области Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. [Настройка решения аналитики журнала действий](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) для рабочей области.

1. [Просмотр журналов действий](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Чтобы быстро переходить на страницу "Обзор решения Аналитика журнала действий", щелкните **log Analytics** .

   ![Параметр журналов Azure Monitor на портале](./media/change-history-report/azure-log-analytics-option.png)

1. При необходимости используйте страницу [Поиска по журналам](../log-analytics/log-analytics-log-search.md) или [портал расширенной аналитики](../azure-monitor/log-query/get-started-portal.md) для формирования запросов и просмотра журналов. Дополнительные сведения об этих двух вариантах см. в разделах о [странице "Поиск по журналам" или портале расширенной аналитики](../azure-monitor/log-query/portals.md).

Ниже приведен запрос, возвращающий новые назначения ролей, упорядоченные по целевому поставщику ресурсов.

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Ниже приведен запрос, возвращающий изменения назначения ролей, отображаемые на схеме.

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationNameValue
| render timechart
```

![Снимок экрана: журналы действий на портале расширенной аналитики](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Следующие шаги
* [Просмотр событий в журнале действий](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitor Subscription Activity with the Azure Activity Log](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (Мониторинг действий подписки с помощью журнала действий Azure)
