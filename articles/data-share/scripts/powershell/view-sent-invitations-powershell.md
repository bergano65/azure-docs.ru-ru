---
title: Сценарий PowerShell. Вывод списка приглашений на обмен данными Azure, отправленных потребителю | Документация Майкрософт
description: Этот сценарий PowerShell принимает приглашения от существующей общей папки данных.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 157b844afd9d59b6c03c7ae8585bc2182370d3a3
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307153"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>Использование PowerShell для получения приглашения для общего доступа к данным

Этот сценарий PowerShell получает приглашения, отправляемые потребителю.

## <a name="sample-script"></a>Пример скрипта
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Command | Примечания |
|---|---|
| [Get-Аздаташареинвитатион](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Получение и перечисление отправленных приглашений на обмен данными. |
|||

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для общего ресурса Azure Data Share можно найти в [примерах PowerShell для общего доступа к данным Azure](../../samples-powershell.md).
