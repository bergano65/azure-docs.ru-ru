---
title: 'Сценарий PowerShell: список приглашений на общий ресурс Azure, отправленных потребителю | Документация Майкрософт'
description: Узнайте, как этот сценарий PowerShell получает приглашения, отправляемые потребителю, и просмотрите пример скрипта, который можно использовать.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 3f0374ba5b98c145359d18143584e23fc543c552
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985624"
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

| Get-Help | Примечания |
|---|---|
| [Get-Аздаташареинвитатион](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Получение и перечисление отправленных приглашений на обмен данными. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для общего ресурса Azure Data Share можно найти в [примерах PowerShell для общего доступа к данным Azure](../../samples-powershell.md).
