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
ms.openlocfilehash: 1ad1d6b9f59fa51b9e27fe5b70ce4e2a5d36f3ad
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300881"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Справочник по командлетам PowerShell для планировщика Azure

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) заменяет планировщик Azure, который выводится из [эксплуатации](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Чтобы продолжить работу с заданиями, настроенными в планировщике, выполните [миграцию на Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) как можно скорее.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Чтобы писать сценарии для создания заданий планировщика и коллекций заданий и управления ими, можно использовать командлеты PowerShell. В этой статье перечислены основные командлеты PowerShell для планировщика Azure со ссылками на соответствующие справочные статьи. Сведения об установке Azure PowerShell для подписки Azure см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview). Дополнительные сведения о [командлетах Azure Resource Manager](/powershell/azure/overview) см. в статье [Использование Azure PowerShell с Azure Resource Manager](../powershell-azure-resource-manager.md).

| Командлет | Описание |
|--------|-------------|
| [Disable-Азсчедулержобколлектион](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Отключает коллекцию заданий. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Включает коллекцию заданий. |
| [Get-Азсчедулержоб](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Получает задания планировщика. |
| [Get-Азсчедулержобколлектион](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Получает коллекции заданий. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Получает журнал заданий. |
| [New-Азсчедулерхттпжоб](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Создает задание HTTP. |
| [New-Азсчедулержобколлектион](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Создает коллекцию заданий. |
| [New-Азсчедулерсервицебускуеуежоб](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Создает задание очереди служебной шины. |
| [New-Азсчедулерсервицебустопикжоб](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Создает задание раздела служебной шины. |
| [New-Азсчедулерсторажекуеуежоб](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Создает задание очереди хранилища. |
| [Remove-Азсчедулержоб](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Удаляет задание планировщика. |
| [Remove-Азсчедулержобколлектион](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Удаляет коллекцию заданий. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Изменяет задание HTTP планировщика. |
| [Set-Азсчедулержобколлектион](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Изменяет коллекцию заданий. |
| [Set-Азсчедулерсервицебускуеуежоб](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Изменяет задание очереди служебной шины. |
| [Set-Азсчедулерсервицебустопикжоб](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Изменяет задание раздела служебной шины. |
| [Set-Азсчедулерсторажекуеуежоб](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Изменяет задание очереди хранилища. |
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
* [Создание и планирование первого задания с помощью портала Azure](scheduler-get-started-portal.md)
* [Справочник по API REST планировщика Azure](https://msdn.microsoft.com/library/mt629143)
