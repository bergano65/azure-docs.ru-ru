---
title: Сценарий PowerShell. Перечисление существующих общих ресурсов в общем ресурсе данных Azure | Документация Майкрософт
description: Этот сценарий PowerShell выводит список и отображает подробные сведения об общих ресурсах.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 53b44441397be6aaa690c75bf362fd16d5a34019
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242896"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>Использование PowerShell для просмотра сведений о отправленной общей папке данных

Этот сценарий PowerShell перечисляет общие ресурсы данных из существующей учетной записи и получает сведения об определенном ресурсе.


## <a name="sample-script"></a>Пример скрипта

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

#Lists all data shares within an account
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName

#Gets details of a specific data share
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Command | Примечания |
|---|---|
| [Get-Аздаташаре](/powershell/module/az.resources/get-az) | Возвращает списки общих ресурсов в учетной записи. |
|||

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для общего ресурса Azure Data Share можно найти в [примерах PowerShell для общего доступа к данным Azure](../../samples-powershell.md).
