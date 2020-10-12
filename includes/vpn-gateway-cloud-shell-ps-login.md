---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "77133604"
---
Откройте консоль PowerShell с повышенными привилегиями.

Если вы используете Azure PowerShell локально, подключитесь к своей учетной записи Azure. Командлет *Connect-AzAccount* запросит учетные данные. После проверки подлинности он загрузит параметры учетной записи, чтобы они были доступны в Azure PowerShell. Если вместо этого используется Azure Cloud Shell, не нужно выполнять команду *Connect-азаккаунт*. Azure Cloud Shell автоматически подключается к учетной записи Azure.

```azurepowershell
Connect-AzAccount
```

Если у вас есть несколько подписок Azure, запросите их список.

```azurepowershell-interactive
Get-AzSubscription
```

Укажите подписку, которую нужно использовать.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```