---
title: Настройка OpenVPN для VPN-шлюза Azure — PowerShell | Документация Майкрософт
description: Узнайте, как настроить OpenVPN для VPN-шлюза Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 609c2ef91fafe0ae955252a594292d861e772f87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002964"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Настройка OpenVPN для VPN-шлюз Azure точка сеть

Эта статья поможет вам настроить **OpenVPN® протокола** на VPN-шлюза Azure. В этой статье предполагается наличие рабочей среды "точка — сеть". В противном случае см. инструкции на шаге 1 по созданию VPN-подключения "точка — сайт".

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="vnet"></a>1. Создание VPN-подключения "— сеть"

Если у вас нет работающей среды "точка — сеть", следуйте инструкциям, чтобы создать ее. См. дополнительные сведения о [создании и настройке VPN-подключения "точка — сеть" с использованием аутентификации на основе собственного сертификата Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 

> [!IMPORTANT]
> SKU уровня "Базовый" не поддерживается для OpenVPN.

## <a name="enable"></a>2. Включение OpenVPN в шлюзе

Включите OpenVPN в шлюзе. Убедитесь, что шлюз уже настроен для подключения "точка — сеть" (IKEv2 или SSTP), прежде чем выполнять следующие команды:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Дальнейшие действия

См. дополнительные сведения о [настройке клиентов OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**«OpenVPN» является товарным знаком OpenVPN Inc.**
