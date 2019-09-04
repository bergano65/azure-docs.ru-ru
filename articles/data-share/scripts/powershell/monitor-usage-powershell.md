---
title: Сценарий PowerShell. Мониторинг использования общей папки данных Azure | Документация Майкрософт
description: Этот сценарий PowerShell получает метрики использования отправленной общей папки данных.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9cc7cbe8a1f523a50c91125802804e564b48a164
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242961"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Использование PowerShell для отслеживания использования отправленных общих ресурсов данных

Этот сценарий PowerShell отслеживает использование данных, перечисляя синхронизацию отправленного общего ресурса данных и получая сведения о конкретной синхронизации.

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

| Command | Примечания |
|---|---|
| [Get-Аздаташаресинчронизатион](/powershell/module/az.resources/get-azdatasharesynchronizationdetails) | Вывод списка синхронизаций в общей папке. |
| [Get-Аздаташаресинчронизатиондетаилс](/powershell/module/az.resources/get-azdatasharesynchronizationdetails) | Возвращает сведения о синхронизации общей папки. |
|||

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для общего ресурса Azure Data Share можно найти в [примерах PowerShell для общего доступа к данным Azure](../../samples-powershell.md).
