---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f6fd4039614dbd7c1a2b2c6ba8403502a6420fe3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185170"
---
Чтобы изменить размер шлюза для текущих номеров SKU (VpnGw1, VpnGw2 и VPNGW3) для обновления до более мощной конфигурации, вы можете использовать командлет PowerShell `Resize-AzVirtualNetworkGateway`. С помощью этого командлета также можно перейти на более низкий уровень SKU. Если вы используете номер SKU шлюза уровня "Базовый", чтобы изменить размер шлюза, [вместо указанного командлета используйте эти инструкции](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize).

В следующем примере PowerShell показано изменение размера шлюза до SKU со значением VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Кроме того, вы можете изменить размер шлюза на портале Azure. Для этого откройте страницу **Конфигурация** для своего шлюза виртуальной сети и выберите другой номер SKU в раскрывающемся списке.