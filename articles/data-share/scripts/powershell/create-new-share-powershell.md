---
title: Сценарий PowerShell. Создание общей папки данных Azure | Документация Майкрософт
description: Этот сценарий PowerShell создает новую общую папку данных в существующей учетной записи общей папки данных.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 54d5dc35cf230b62cbe5f8c3cb4a1dc81eccf1e3
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307247"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Создание общей папки данных в Azure с помощью PowerShell

Этот сценарий PowerShell создает новую общую папку данных в существующей учетной записи общей папки данных.

## <a name="sample-script"></a>Пример скрипта

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Command | Примечания |
|---|---|
| [New-Аздаташаре](/powershell/module/az.datashare/new-azdatashare?view=azps-2.6.0) | Создает общую папку данных. |
|||

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для общего ресурса Azure Data Share можно найти в [примерах PowerShell для общего доступа к данным Azure](../../samples-powershell.md).
