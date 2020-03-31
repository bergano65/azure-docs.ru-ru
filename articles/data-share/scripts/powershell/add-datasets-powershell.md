---
title: 'Скрипт PowerShell: Добавьте набор данных капли в раздел данных Azure (ru) Документы Майкрософт'
description: Этот скрипт PowerShell добавляет набор данных капли к существующей доле.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 95a10b112c9f6448c437f20ee95f808632a31d2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307296"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Используйте PowerShell для создания совместного использования данных в Azure

Этот скрипт PowerShell добавляет набор данных капли в существующий обмен данными.

## <a name="sample-script"></a>Пример скрипта

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$blobDatasetName = "<Dataset name>"
$blobContainer = "<Blob container name>"
$blobFilePath = "<Blob file path>"
$storageAccountResourceId = "<Storage account resource id>"

#Add a blob dataset to the datashare
New-AzDataShareDataSet -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $blobDataSetName -StorageAccountResourceId $storageAccountResourceId -FilePath $blobFilePath

```


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Команда | Примечания |
|---|---|
| [Новое-AzDataShareDataDataSet](/powershell/module/az.datashare/new-azdatasharedataset?view=azps-2.6.0) | Добавляет набор данных в общий доступ данных. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные образцы скриптов Azure Data Share PowerShell можно найти в [образцах Azure Data Share PowerShell.](../../samples-powershell.md)
