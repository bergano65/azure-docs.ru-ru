---
title: Сценарий PowerShell. Принять приглашение из общей папки данных Azure | Документация Майкрософт
description: Этот сценарий PowerShell принимает приглашения от существующей общей папки данных.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9ef35e0865a7c5bbfb4accc058a8d63bb6f1e80b
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243078"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>Использование PowerShell для принятия приглашения для общего доступа к данным

Этот сценарий PowerShell принимает приглашения, отправленные потребителю.

## <a name="sample-script"></a>Пример скрипта
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Command | Примечания |
|---|---|
| [Get-Аздаташареинвитатион](/powershell/module/az.resources/get-azdatashareinvitation) | Получение и перечисление отправленных приглашений на обмен данными. |
| [New-Аздаташаресубскриптион](/powershell/module/az.resources/get-azdatashareinvitation) | Создайте подписку на общую папку данных. |
|||

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для общего ресурса Azure Data Share можно найти в [примерах PowerShell для общего доступа к данным Azure](../../samples-powershell.md).

