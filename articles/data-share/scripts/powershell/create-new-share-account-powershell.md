---
title: 'Скрипт PowerShell: Создание новой учетной записи обмена данными Azure (ru) Документы Майкрософт'
description: Этот скрипт PowerShell создает новую учетную запись обмена данными.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: c3852dd5f1d3d3df8a982716ce5dab9426782869
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307275"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>Используйте PowerShell для создания учетной записи обмена данными в Azure

Этот скрипт PowerShell создает новую учетную запись обмена данными. 

## <a name="sample-script"></a>Пример скрипта

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name"
$location = "<Location>"
$dataShareAccountName = "<Data share account name>"

# Create a new Azure Data Share account
New-AzDataShareAccount -ResourceGroupName $resourceGroupName -Name $dataShareAccountName -Location $location
```

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Команда | Примечания |
|---|---|
| [Новый АзДатаДоляКл](/powershell/module/az.datashare/new-azdatashareaccount?view=azps-2.6.0) | Создает учетную запись обмена данными. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные образцы скриптов Azure Data Share PowerShell можно найти в [образцах Azure Data Share PowerShell.](../../samples-powershell.md)
