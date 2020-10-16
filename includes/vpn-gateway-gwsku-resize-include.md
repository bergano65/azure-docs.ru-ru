---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "73495781"
---
Вы можете использовать командлет `Resize-AzVirtualNetworkGateway` PowerShell для перехода на более раннюю или более позднюю версии SKU (Generation1 или Generation2). Все номера SKU VpnGw можно изменить, за исключением номеров SKU уровня "Базовый". Если вы используете номер SKU шлюза уровня "Базовый", чтобы изменить размер шлюза, [вместо указанного командлета используйте эти инструкции](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize).

В следующем примере PowerShell показано изменение размера шлюза до SKU со значением VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Кроме того, вы можете изменить размер шлюза на портале Azure. Для этого откройте страницу **Конфигурация** для своего шлюза виртуальной сети и выберите другой номер SKU в раскрывающемся списке.
