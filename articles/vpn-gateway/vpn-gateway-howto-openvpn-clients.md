---
title: Как настроить клиенты OpenVPN для VPN-шлюза Azure | Документация Майкрософт
description: Узнайте, как настроить клиенты Опенвпн Protocol для VPN-шлюза Azure, включая клиенты Windows, Mac и Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 136dcb0b2d1740aa5cadbd716b4a8386ad5cf486
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926218"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Настройка клиентов Опенвпн для VPN-шлюза Azure

Эта статья поможет вам настроить **клиенты &reg; протокола опенвпн** .

## <a name="before-you-begin"></a>Подготовка

Убедитесь, что шаги по настройке OpenVPN для вашего VPN-шлюза выполнены. Дополнительные сведения см. в разделе [Настройка OpenVPN для VPN-шлюза Azure](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Дальнейшие действия

Если вы хотите, чтобы VPN-клиенты могли получать доступ к ресурсам в другой виртуальной сети, следуйте инструкциям в статье " [Виртуальная](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) сеть-vnet", чтобы настроить подключение между виртуальными сетями. Обязательно включите протокол BGP на шлюзах и соединениях, иначе трафик не будет считываться.

**"Опенвпн" является товарным знаком Опенвпн Inc.**
