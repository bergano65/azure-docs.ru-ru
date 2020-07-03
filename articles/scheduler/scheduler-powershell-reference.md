---
title: Справочник по командлетам PowerShell
description: Сведения о командлетах PowerShell для планировщика Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 577e2128cf7e4e9f914ec5504917053acb3c19d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898495"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Справочник по командлетам PowerShell для планировщика Azure

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) заменяет планировщик Azure, который выводится из [эксплуатации](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Чтобы продолжить работу с заданиями, настроенными в планировщике, выполните [миграцию на Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) как можно скорее. 
>
> Планировщик больше не доступен в портал Azure, но командлеты PowerShell для [REST API](/rest/api/scheduler) и [планировщика Azure](scheduler-powershell-reference.md) в настоящее время остаются доступными, чтобы можно было управлять заданиями и коллекциями заданий.

Чтобы писать сценарии для создания заданий планировщика и коллекций заданий и управления ими, можно использовать командлеты PowerShell. В этой статье перечислены основные командлеты PowerShell для планировщика Azure со ссылками на соответствующие справочные статьи. Сведения об установке Azure PowerShell для подписки Azure см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview). Дополнительные сведения о [командлетах Azure Resource Manager](/powershell/azure/overview)см. в разделе [использование Azure PowerShell с Azure Resource Manager](../powershell-azure-resource-manager.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Командлет | Описание |
|--------|-------------|
| [Disable-Азсчедулержобколлектион](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Отключает коллекцию заданий. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Включает коллекцию заданий. |
| [Get-Азсчедулержоб](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Получает задания планировщика. |
| [Get-Азсчедулержобколлектион](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Получает коллекции заданий. |
| [Get-Азсчедулержобхистори](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Получает журнал заданий. |
| [New-Азсчедулерхттпжоб](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Создает задание HTTP. |
| [New-Азсчедулержобколлектион](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Создает коллекцию заданий. |
| [New-Азсчедулерсервицебускуеуежоб](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Создает задание очереди служебной шины. |
| [New-Азсчедулерсервицебустопикжоб](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Создает задание раздела служебной шины. |
| [New-Азсчедулерсторажекуеуежоб](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Создает задание очереди хранилища. |
| [Remove-Азсчедулержоб](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Удаляет задание планировщика. |
| [Remove-Азсчедулержобколлектион](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Удаляет коллекцию заданий. |
| [Set-Азсчедулерхттпжоб](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Изменяет задание HTTP планировщика. |
| [Set-Азсчедулержобколлектион](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Изменяет коллекцию заданий. |
| [Set-Азсчедулерсервицебускуеуежоб](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Изменяет задание очереди служебной шины. |
| [Set-Азсчедулерсервицебустопикжоб](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Изменяет задание раздела служебной шины. |
| [Set-Азсчедулерсторажекуеуежоб](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Изменяет задание очереди хранилища. |
||| 

Для получения дополнительных сведений можно выполнить любой из этих командлетов: 

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>Дальнейшие шаги

* [Основные понятия, терминология и иерархия сущностей планировщика Azure](scheduler-concepts-terms.md)
* [Ограничения, значения по умолчанию и коды ошибок планировщика Azure](scheduler-limits-defaults-errors.md)
* [Справочник по API REST планировщика Azure](/rest/api/scheduler)