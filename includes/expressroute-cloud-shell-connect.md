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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67185020"
---
 Если вы используете оболочку облака Azure, вы автоматически впишетесь в свою учетную запись Azure после нажатия кнопки «Попробуй». Чтобы войти в локально, откройте консоль PowerShell с повышенными привилегиями и запустите cmdlet для подключения.

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