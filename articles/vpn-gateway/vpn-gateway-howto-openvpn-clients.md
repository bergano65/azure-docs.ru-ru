---
title: Как настроить клиенты OpenVPN для VPN-шлюза Azure | Документация Майкрософт
description: Действия по настройке клиентов OpenVPN для VPN-шлюза Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 09ff3ccebad0baa4148e68995254c818a29d7bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066084"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Настройка openVPN клиентов для Azure VPN Gateway

Эта статья поможет настроить клиентов ** &reg; OpenVPN Protocol.**

## <a name="before-you-begin"></a>Перед началом

Убедитесь, что шаги по настройке OpenVPN для вашего VPN-шлюза выполнены. Дополнительные сведения см. в разделе [Настройка OpenVPN для VPN-шлюза Azure](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Дальнейшие действия

Если вы хотите, чтобы клиенты VPN могли получить доступ к ресурсам в другом VNet, то следуйте инструкциям в статье [VNet-to-VNet,](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) чтобы настроить соединение vnet-vnet. Обязательно включите протокол BGP на шлюзах и соединениях, иначе трафик не будет считываться.

**"OpenVPN" является торговой маркой openVPN Inc.**
