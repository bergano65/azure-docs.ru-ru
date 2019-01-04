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
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852377"
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