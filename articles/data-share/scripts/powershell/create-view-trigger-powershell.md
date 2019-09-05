---
title: Сценарий PowerShell. Создание и просмотр триггеров моментальных снимков общих ресурсов Azure | Документация Майкрософт
description: Этот сценарий PowerShell создает и получает триггеры общего доступа к моментальным снимкам.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 4f3148a4d5bd0d39ccfcf7e92e80300a7e19effa
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307211"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Использование PowerShell для отслеживания использования отправленных общих ресурсов данных

Этот сценарий PowerShell создает и получает триггеры общего доступа к моментальным снимкам.

## <a name="sample-script"></a>Пример скрипта

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$subscriptionName = "<Share subscription name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new snapshot trigger
New-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName $subscriptionName -Name $dataShareName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List snapshot triggers
Get-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName $subscriptionName  -Name $dataShareName

#Get a specific share snapshot trigger
Get-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName -Name $dataShareName
```

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Command | Примечания |
|---|---|
| [New-Аздаташаретригжер](/powershell/module/az.datashare/new-azdatasharetrigger?view=azps-2.6.0) | Создайте триггер моментального снимка общего ресурса. |
| [Get-Аздаташаретригжер](/powershell/module/az.datashare/get-azdatasharesynchronizationsetting?view=azps-2.6.0) | Возвращает параметры синхронизации общей папки. |
|||

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для общего ресурса Azure Data Share можно найти в [примерах PowerShell для общего доступа к данным Azure](../../samples-powershell.md).
