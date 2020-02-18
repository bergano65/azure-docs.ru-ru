---
title: 'Как настроить Опенвпн на VPN-шлюзе Azure: PowerShell'
description: Узнайте, как настроить OpenVPN для VPN-шлюза Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 7505420cc31fe751ecc0c114a89fea0734cbc6cf
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162413"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Настройка Опенвпн для VPN-шлюза типа "точка — сеть" Azure

Эта статья поможет вам настроить **протокол опенвпн®** на VPN-шлюзе Azure. В этой статье предполагается наличие рабочей среды "точка — сеть". В противном случае см. инструкции на шаге 1 по созданию VPN-подключения "точка — сайт".



## <a name="vnet"></a>1. Создание VPN-подключения типа "точка — сеть"

Если у вас нет работающей среды "точка — сеть", следуйте инструкциям, чтобы создать ее. См. дополнительные сведения о [создании и настройке VPN-подключения "точка — сеть" с использованием аутентификации на основе собственного сертификата Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 

> [!IMPORTANT]
> SKU уровня "Базовый" не поддерживается для OpenVPN.

## <a name="enable"></a>2. Включите Опенвпн в шлюзе.

Включите OpenVPN в шлюзе. Убедитесь, что шлюз уже настроен для подключения "точка — сеть" (IKEv2 или SSTP), прежде чем выполнять следующие команды:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Дальнейшие действия

См. дополнительные сведения о [настройке клиентов OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"Опенвпн" является товарным знаком Опенвпн Inc.**
