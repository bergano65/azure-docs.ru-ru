---
title: 'Скрипт PowerShell: Мониторинг использования совместного использования данных Azure (ru) Документы Майкрософт'
description: Этот скрипт PowerShell получает метрики использования отправленного обмена данными.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 0a4084d309dd0160970f1c03540705b310eb8e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307206"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Используйте PowerShell для мониторинга использования отправленного обмена данными

Этот скрипт PowerShell отслеживает использование данных, перечисляя синхронизацию отправленного обмена данными и получая сведения о конкретной синхронизации.

## <a name="sample-script"></a>Пример скрипта


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$synchronizationId = "<Azure synchronization id>"

# List synchronizations on a share
Get-AzDataShareSynchronization -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName

#Get details of a specific synchronization
Get-AzDataShareSynchronizationDetails -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -SynchronizationId $synchronizationId
```


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Команда | Примечания |
|---|---|
| [Get-AzDataShareСинхронизация](/powershell/module/az.datashare/get-azdatasharesynchronization?view=azps-2.6.0) | Список синхронизаций на акции. |
| [Get-AzDataShareСинхронизацияПодробностиДетали](/powershell/module/az.datashare/get-azdatasharesynchronizationdetail?view=azps-2.6.0) | Получает детали синхронизации синхронизации акций. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные образцы скриптов Azure Data Share PowerShell можно найти в [образцах Azure Data Share PowerShell.](../../samples-powershell.md)
