---
title: Включить имя файла
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "73495781"
---
Вы можете использовать `Resize-AzVirtualNetworkGateway` командлет PowerShell для обновления или понижения уровня Generation1 или GENERATION2 SKU (все номера SKU VpnGw можно изменять, за исключением основных SKU). Если вы используете номер SKU шлюза уровня "Базовый", чтобы изменить размер шлюза, [вместо указанного командлета используйте эти инструкции](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize).

В следующем примере PowerShell показано изменение размера шлюза до SKU со значением VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Кроме того, вы можете изменить размер шлюза на портале Azure. Для этого откройте страницу **Конфигурация** для своего шлюза виртуальной сети и выберите другой номер SKU в раскрывающемся списке.
