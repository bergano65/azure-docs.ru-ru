---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 853bd32cf3ea97571929d54fb7d3ba04bde16b81
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185156"
---
Прежде чем приступить к настройке, необходимо войти в учетную запись Azure. Командлет запрашивает учетные данные входа для вашей учетной записи Azure. После выполнения входа он скачивает параметры учетной записи, чтобы они были доступны в Azure PowerShell. Дополнительные сведения см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](../articles/powershell-azure-resource-manager.md).

Чтобы войти, откройте консоль PowerShell с повышенными привилегиями и подключитесь к своей учетной записи. Для подключения используйте следующий пример кода:

```powershell
Connect-AzAccount
```

Если у вас есть несколько подписок Azure, проверьте подписки для этой учетной записи.

```powershell
Get-AzSubscription
```

Укажите подписку, которую нужно использовать.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
