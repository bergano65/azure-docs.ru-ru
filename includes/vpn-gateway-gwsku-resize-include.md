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
ms.openlocfilehash: 081352a23e6a0d8f9e2daa77eca1f8ac85172ff6
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418234"
---
Чтобы изменить размер шлюза для текущих номеров SKU (VpnGw1, VpnGw2 и VPNGW3) для обновления до более мощной конфигурации, вы можете использовать командлет PowerShell `Resize-AzVirtualNetworkGateway`. С помощью этого командлета также можно перейти на более низкий уровень SKU. Если вы используете номер SKU шлюза уровня "Базовый", чтобы изменить размер шлюза, [вместо указанного командлета используйте эти инструкции](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize).

В следующем примере PowerShell показано изменение размера шлюза до SKU со значением VpnGw2.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Кроме того, вы можете изменить размер шлюза на портале Azure. Для этого откройте страницу **Конфигурация** для своего шлюза виртуальной сети и выберите другой номер SKU в раскрывающемся списке.