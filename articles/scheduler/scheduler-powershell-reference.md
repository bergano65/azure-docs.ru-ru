---
title: Ссылка на смдлеты PowerShell
description: Сведения о командлетах PowerShell для планировщика Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 577e2128cf7e4e9f914ec5504917053acb3c19d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898495"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Справочник по командлетам PowerShell для планировщика Azure

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) заменяет Azure Scheduler, который [в настоящее время удался.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Чтобы продолжить работу с заданиями, настроенными в Scheduler, пожалуйста, [перейти на azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) как можно скорее. 
>
> Планировщик больше не доступен на портале Azure, но на данный момент доступны смдлеты [REST API](/rest/api/scheduler) и [Azure Scheduler PowerShell,](scheduler-powershell-reference.md) чтобы вы могли управлять своими рабочими местами и коллекциями заданий.

Чтобы писать сценарии для создания заданий планировщика и коллекций заданий и управления ими, можно использовать командлеты PowerShell. В этой статье перечислены основные командлеты PowerShell для планировщика Azure со ссылками на соответствующие справочные статьи. Сведения об установке Azure PowerShell для подписки Azure см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview). Для получения дополнительной информации о [cmdlets менеджера ресурсов Azure](/powershell/azure/overview)см. [Using Azure PowerShell with Azure Resource Manager](../powershell-azure-resource-manager.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Командлет | Описание |
|--------|-------------|
| [Отключить-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Отключает коллекцию заданий. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Включает коллекцию заданий. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Получает задания планировщика. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Получает коллекции заданий. |
| [Get-AzSchedulerJobИстория](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Получает журнал заданий. |
| [Новый-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Создает задание HTTP. |
| [Новый-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Создает коллекцию заданий. |
| [Новый-AzSchedulerServiceBusКиКейлРабота](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Создает задание очереди служебной шины. |
| [Новый-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Создает задание раздела служебной шины. |
| [Новый-AzПлантлерХранениеКиРабота](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Создает задание очереди хранилища. |
| [Удалить-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Удаляет задание планировщика. |
| [Удалить-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Удаляет коллекцию заданий. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Изменяет задание HTTP планировщика. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Изменяет коллекцию заданий. |
| [Set-AzSchedulerServiceBusКикейлРабота](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Изменяет задание очереди служебной шины. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Изменяет задание раздела служебной шины. |
| [Set-AzПлантлерХранениеКиРаботаРабота](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Изменяет задание очереди хранилища. |
||| 

Для получения дополнительных сведений можно выполнить любой из этих командлетов: 

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>Дальнейшие действия

* [Основные понятия, терминология и иерархия сущностей планировщика Azure](scheduler-concepts-terms.md)
* [Ограничения, значения по умолчанию и коды ошибок планировщика Azure](scheduler-limits-defaults-errors.md)
* [Справочник по API REST планировщика Azure](/rest/api/scheduler)