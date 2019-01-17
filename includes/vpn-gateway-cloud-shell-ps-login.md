---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fee97b29f24d8bb4f50a2929c3ceb33af85a5e21
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201488"
---
Откройте консоль PowerShell с повышенными привилегиями.



Если вы используете Azure PowerShell локально, подключитесь к своей учетной записи Azure. Командлет *Connect-AzureRmAccount* запросит учетные данные. После проверки подлинности он загрузит параметры учетной записи, чтобы они были доступны в Azure PowerShell. Если вы не запускаете PowerShell локально, а вместо этого используете пробную версию Azure Cloud Shell в браузере, можете пропустить этот шаг. Вы автоматически подключитесь к учетной записи Azure.

```azurepowershell
Connect-AzureRmAccount
```

Если у вас есть несколько подписок Azure, запросите их список.

```azurepowershell-interactive
Get-AzureRmSubscription
```

Укажите подписку, которую нужно использовать.

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```