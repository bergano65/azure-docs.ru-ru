---
title: Справочник по командлетам PowerShell для планировщика Azure
description: Сведения о командлетах PowerShell для планировщика Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: daf960bec0fac5f0c96749c219304bc77a4ba905
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358549"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Справочник по командлетам PowerShell для планировщика Azure

> [!IMPORTANT]
> Служба [Azure Logic Apps](../logic-apps/logic-apps-overview.md) заменяет планировщик Azure, который выводится из эксплуатации. Для планирования заданий [попробуйте использовать Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Чтобы писать сценарии для создания заданий планировщика и коллекций заданий и управления ими, можно использовать командлеты PowerShell. В этой статье перечислены основные [командлеты PowerShell для планировщика Azure](/powershell/module/azurerm.scheduler) со ссылками на соответствующие справочные статьи. Сведения об установке Azure PowerShell для подписки Azure см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview). Дополнительные сведения о [командлетах Azure Resource Manager](/powershell/azure/overview) см. в статье [Использование Azure PowerShell с Azure Resource Manager](../powershell-azure-resource-manager.md).

| Командлет | ОПИСАНИЕ |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azschedulerjobcollection) |Отключает коллекцию заданий. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azschedulerjobcollection) |Включает коллекцию заданий. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azschedulerjob) |Получает задания планировщика. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azschedulerjobcollection) |Получает коллекции заданий. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azschedulerjobhistory) |Получает журнал заданий. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azschedulerhttpjob) |Создает задание HTTP. |
| [Новый AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azschedulerjobcollection) |Создает коллекцию заданий. |
| [Новый AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azschedulerservicebusqueuejob) | Создает задание очереди служебной шины. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azschedulerservicebustopicjob) |Создает задание раздела служебной шины. |
| [Новый AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azschedulerstoragequeuejob) |Создает задание очереди хранилища. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azschedulerjob) |Удаляет задание планировщика. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azschedulerjobcollection) |Удаляет коллекцию заданий. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azschedulerhttpjob) |Изменяет задание HTTP планировщика. |
| [SET-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azschedulerjobcollection) |Изменяет коллекцию заданий. |
| [SET-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azschedulerservicebusqueuejob) |Изменяет задание очереди служебной шины. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azschedulerservicebustopicjob) |Изменяет задание раздела служебной шины. |
| [SET-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azschedulerstoragequeuejob) |Изменяет задание очереди хранилища. |
||| 

Для получения дополнительных сведений можно выполнить любой из этих командлетов: 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>См. также

* [Что такое планировщик Azure?](scheduler-intro.md)
* [Основные понятия, терминология и иерархия сущностей](scheduler-concepts-terms.md)
* [Создание и планирование первого задания — портал Azure](scheduler-get-started-portal.md)
* [Справочник по API REST планировщика Azure](https://msdn.microsoft.com/library/mt629143)
