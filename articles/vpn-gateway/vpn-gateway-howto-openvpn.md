---
title: 'Как настроить Опенвпн на VPN-шлюзе Azure: PowerShell'
description: Узнайте, как использовать PowerShell для включения протокола Опенвпн на VPN-шлюзе Azure для среды типа "точка — сеть".
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 1e2f7f754ae9a1547d6543dba65c69511ab7ceb1
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99624918"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Настройка Опенвпн для VPN-шлюза типа "точка — сеть" Azure

Эта статья поможет вам настроить **протокол опенвпн®** на VPN-шлюзе Azure. В этой статье предполагается наличие рабочей среды "точка — сеть". В противном случае см. инструкции на шаге 1 по созданию VPN-подключения "точка — сайт".



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. Создание VPN-подключения типа "точка — сеть"

Если у вас нет работающей среды "точка — сеть", следуйте инструкциям, чтобы создать ее. См. дополнительные сведения о [создании и настройке VPN-подключения "точка — сеть" с использованием аутентификации на основе собственного сертификата Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 

> [!IMPORTANT]
> SKU уровня "Базовый" не поддерживается для OpenVPN.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. Включите Опенвпн в шлюзе.

Включите OpenVPN в шлюзе.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Дальнейшие действия

См. дополнительные сведения о [настройке клиентов OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"Опенвпн" является товарным знаком Опенвпн Inc.**
