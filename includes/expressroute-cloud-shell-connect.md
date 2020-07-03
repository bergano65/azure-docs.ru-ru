---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67185020"
---
 Если вы используете Azure Cloud Shell, вы автоматически входите в учетную запись Azure после нажатия кнопки "попробовать". Чтобы войти в систему локально, откройте консоль PowerShell с повышенными привилегиями и запустите командлет для подключения.

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