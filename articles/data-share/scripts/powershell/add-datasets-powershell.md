---
title: Сценарий PowerShell. Добавление набора данных большого двоичного объекта в общую папку данных Azure | Документация Майкрософт
description: Этот сценарий PowerShell добавляет набор данных большого двоичного объекта в существующую общую папку.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 95a10b112c9f6448c437f20ee95f808632a31d2d
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307296"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Создание общей папки данных в Azure с помощью PowerShell

Этот сценарий PowerShell добавляет набор данных большого двоичного объекта в существующую общую папку данных.

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

| Command | Примечания |
|---|---|
| [New-Аздаташаредатасет](/powershell/module/az.datashare/new-azdatasharedataset?view=azps-2.6.0) | Добавляет набор данных в общую папку данных. |
|||

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для общего ресурса Azure Data Share можно найти в [примерах PowerShell для общего доступа к данным Azure](../../samples-powershell.md).
