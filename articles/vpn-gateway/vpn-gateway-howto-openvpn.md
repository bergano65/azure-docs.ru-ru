---
title: 'Как настроить Опенвпн на VPN-шлюзе Azure: PowerShell'
description: Узнайте, как настроить OpenVPN для VPN-шлюза Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: de8d03467b5e44df1b9069c6db31d496785ff32e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84983856"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Настройка Опенвпн для VPN-шлюза типа "точка — сеть" Azure

Эта статья поможет вам настроить **протокол опенвпн®** на VPN-шлюзе Azure. В этой статье предполагается наличие рабочей среды "точка — сеть". В противном случае см. инструкции на шаге 1 по созданию VPN-подключения "точка — сайт".



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. Создание VPN-подключения типа "точка — сеть"

Если у вас нет работающей среды "точка — сеть", следуйте инструкциям, чтобы создать ее. См. дополнительные сведения о [создании и настройке VPN-подключения "точка — сеть" с использованием аутентификации на основе собственного сертификата Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 

> [!IMPORTANT]
> SKU уровня "Базовый" не поддерживается для OpenVPN.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. Включите Опенвпн в шлюзе.

Включите OpenVPN в шлюзе. Убедитесь, что шлюз уже настроен для подключения "точка — сеть" (IKEv2 или SSTP), прежде чем выполнять следующие команды:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Дальнейшие шаги

См. дополнительные сведения о [настройке клиентов OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"Опенвпн" является товарным знаком Опенвпн Inc.**
