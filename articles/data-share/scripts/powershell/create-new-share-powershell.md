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
ms.openlocfilehash: c76dfea83d0f736dbd48239cc3be3496a88ec8a2
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243000"
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
| [New-Аздаташаре](/powershell/module/az.resources/new-azdatashare) | Создает общую папку данных. |
|||

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для общего ресурса Azure Data Share можно найти в [примерах PowerShell для общего доступа к данным Azure](../../samples-powershell.md).
