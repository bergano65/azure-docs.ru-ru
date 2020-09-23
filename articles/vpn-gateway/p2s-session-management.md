---
title: VPN-шлюз Azure. Управление сеансами VPN типа "точка — сеть"
description: Эта статья поможет вам просмотреть и отключить сеансы VPN типа "точка — сеть".
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 08/26/2020
ms.author: cherylmc
ms.openlocfilehash: 3c9023e23795fd51e5519727a0ab174dc6f75830
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938999"
---
# <a name="point-to-site-vpn-session-management"></a>Управление сеансами VPN типа "точка — сеть"

Шлюзы виртуальной сети Azure предоставляют простой способ просмотра и отключения текущих сеансов VPN типа "точка — сеть". Эта статья поможет вам просмотреть и отключить текущие сеансы.

>[!NOTE]
>Состояние сеанса обновляется каждые 5 минут. Он не обновляется немедленно.
>

## <a name="portal"></a>Портал

Чтобы просмотреть и отключить сеанс на портале, выполните следующие действия.

1. Перейдите к VPN-шлюзу.
1. В разделе **мониторинг** выберите **сеансы подключения "точка — сеть**".

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="Пример на портале":::
1. Все текущие сеансы можно просмотреть в windowpane.
1. Выберите **"..."** для сеанса, который нужно отключить, а затем выберите **Отключить**.

## <a name="powershell"></a>PowerShell

Чтобы просмотреть и отключить сеанс с помощью PowerShell, выполните следующие действия.

1. Выполните следующую команду PowerShell, чтобы вывести список активных сеансов:

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. Скопируйте **впнконнектионид** сеанса, который нужно отключить.

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="Пример PowerShell":::
1. Чтобы отключить сеанс, выполните следующую команду:

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о подключениях типа "точка — сеть" см. в статье [о VPN](point-to-site-about.md)-подключении "точка — сеть".
