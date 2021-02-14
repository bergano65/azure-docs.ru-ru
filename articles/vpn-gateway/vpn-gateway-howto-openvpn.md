---
title: Настройка Опенвпн на VPN-шлюзе Azure
description: Узнайте, как использовать PowerShell для включения протокола Опенвпн на VPN-шлюзе Azure для среды типа "точка — сеть".
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 4b48e98ab35b620030b15165d4c9341c0f11b440
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393281"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Настройка Опенвпн для VPN-шлюза типа "точка — сеть" Azure

Эта статья поможет вам настроить **протокол опенвпн®** на VPN-шлюзе Azure. Можно использовать либо портал, либо инструкции PowerShell.

## <a name="prerequisites"></a>Предварительные требования

* В этой статье предполагается наличие рабочей среды "точка — сеть". Если это не так, создайте его с помощью одного из следующих методов.

  * [Портал-создание "точка — сеть"](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell. Создание "точка — сеть"](vpn-gateway-howto-point-to-site-rm-ps.md)

* Убедитесь, что VPN-шлюз не использует номер SKU "базовый". SKU уровня "Базовый" не поддерживается для OpenVPN.

## <a name="portal"></a>Портал

1. На портале перейдите к **шлюзу виртуальной сети — > конфигурация "точка — сеть**".
1. В раскрывающемся списке **Тип туннеля** выберите **опенвпн (SSL)** .

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Выберите Опенвпн SSL из раскрывающегося списка.":::
1. Сохраните изменения и продолжайте **дальнейшие действия**.

## <a name="enable-openvpn-on-your-gateway-using-powershell"></a>Включите Опенвпн на шлюзе с помощью PowerShell.

1. Включите Опенвпн на шлюзе, используя следующий пример:

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Перейдите к **следующим шагам**.

## <a name="next-steps"></a>Следующие шаги

См. дополнительные сведения о [настройке клиентов OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"Опенвпн" является товарным знаком Опенвпн Inc.**
