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
ms.openlocfilehash: 6314bd348c22c901001b88eda6875181a2f69df4
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307123"
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
| [Get-Аздаташаре](/powershell/module/az.datashare/get-azdatashare?view=azps-2.6.0) | Возвращает списки общих ресурсов в учетной записи. |
|||

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для общего ресурса Azure Data Share можно найти в [примерах PowerShell для общего доступа к данным Azure](../../samples-powershell.md).
