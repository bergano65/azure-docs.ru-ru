---
title: 'Скрипт PowerShell: создание нового совместного использования данных Azure (ru) Документы Майкрософт'
description: Этот скрипт PowerShell создает новый обмен данными в существующей учетной записи обмена данными.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 54d5dc35cf230b62cbe5f8c3cb4a1dc81eccf1e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307247"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Используйте PowerShell для создания совместного использования данных в Azure

Этот скрипт PowerShell создает новый обмен данными в существующей учетной записи «Общий доступ к данным».

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

| Команда | Примечания |
|---|---|
| [Новый АзДатаДоля](/powershell/module/az.datashare/new-azdatashare?view=azps-2.6.0) | Создает обмен данными. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные образцы скриптов Azure Data Share PowerShell можно найти в [образцах Azure Data Share PowerShell.](../../samples-powershell.md)
