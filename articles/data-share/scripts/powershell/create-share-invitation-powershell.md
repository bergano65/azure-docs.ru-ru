---
title: 'Скрипт PowerShell: Создание приглашения на совместное использование данных Azure Документы Майкрософт'
description: Этот скрипт PowerShell отправляет приглашение на обмен данными.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9fd8d6428e94007002d524d9ade99f6b368b8201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307234"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Используйте PowerShell для мониторинга использования отправленного обмена данными

Этот скрипт PowerShell создает приглашение для обмена данными.

## <a name="sample-script"></a>Пример скрипта


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Команда | Примечания |
|---|---|
| [Новое-AzDataShareInvitation](/powershell/module/az.datashare/new-azdatashareinvitation?view=azps-2.6.0) | Создайте приглашение на совместное использование данных. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные образцы скриптов Azure Data Share PowerShell можно найти в [образцах Azure Data Share PowerShell.](../../samples-powershell.md)
