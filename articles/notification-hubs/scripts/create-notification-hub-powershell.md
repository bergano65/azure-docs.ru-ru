---
title: Создание концентратора уведомлений Azure с помощью PowerShell Документы Майкрософт
description: Узнайте, как использовать скрипт PowerShell для создания концентратора уведомлений Azure.
author: dimazaid
manager: femila
services: notification-hubs
editor: sethmanheim
ms.service: notification-hubs
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2020
ms.author: dimazaid
ms.openlocfilehash: 01749e1ae5c29a7e77b2143939e25cf1ccd1aec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263985"
---
# <a name="use-powershell-to-create-an-azure-notification-hub"></a>Создание центра уведомлений Azure с помощью PowerShell

Этот сценарий PowerShell создает пример центра уведомлений Azure. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure.** Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/notification-hubs/create-notification-hub/create-notification-hub.ps1 "Create a notification hub")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера скрипта можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды:

| Команда | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzNotificationHubsNamespace](/powershell/module/az.notificationhubs/new-aznotificationhubsnamespace) | Создает пространство имен для центра уведомлений. |
| [New-AzNotificationHub](/powershell/module/az.notificationhubs/new-aznotificationhub) | Создает центр уведомлений. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).
