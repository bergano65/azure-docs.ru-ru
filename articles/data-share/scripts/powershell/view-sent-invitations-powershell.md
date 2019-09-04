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
ms.openlocfilehash: 1304b478bd07ed61293b668badee56338a9cab5a
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242909"
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
| [Get-Аздаташареинвитатион](/powershell/module/az.resources/get-azdatashareinvitation) | Получение и перечисление отправленных приглашений на обмен данными. |
|||

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для общего ресурса Azure Data Share можно найти в [примерах PowerShell для общего доступа к данным Azure](../../samples-powershell.md).
