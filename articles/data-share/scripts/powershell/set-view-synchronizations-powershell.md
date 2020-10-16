---
title: 'Сценарий PowerShell: Настройка и Просмотр параметров синхронизации общего ресурса Azure | Документация Майкрософт'
description: Этот сценарий PowerShell задает и получает общие параметры синхронизации.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 0ced66c1d3023b7edb3220b45e24d3eb54098df8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86512492"
---
# <a name="use-powershell-to-set-azure-data-share-sychronization-settings"></a>Настройка параметров синхронизации общего ресурса данных Azure с помощью PowerShell

Этот сценарий PowerShell задает и получает общие параметры синхронизации.

## <a name="sample-script"></a>Пример скрипта


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$settingName = "<Synchronization setting name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new synchronization setting
New-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $settingName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List share synchronization settings
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName 

#Get a specific share synchronization setting
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName -Name $settingName  
```

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Get-Help | Примечания |
|---|---|
| [New-Аздаташаресинчронизатионсеттинг](/powershell/module/az.datashare/new-azdatasharesynchronizationsetting?view=azps-2.6.0) | Создайте синхронизацию общего ресурса. |
| [Get-Аздаташаресинчронизатионсеттинг](/powershell/module/az.datashare/get-azdatasharesynchronizationsetting?view=azps-2.6.0) | Возвращает параметры синхронизации общей папки. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для общего ресурса Azure Data Share можно найти в [примерах PowerShell для общего доступа к данным Azure](../../samples-powershell.md).
