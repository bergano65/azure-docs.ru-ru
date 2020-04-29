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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066084"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Настройка клиентов Опенвпн для VPN-шлюза Azure

Эта статья поможет вам настроить **клиенты &reg; протокола опенвпн** .

## <a name="before-you-begin"></a>Подготовка к работе

Убедитесь, что шаги по настройке OpenVPN для вашего VPN-шлюза выполнены. Дополнительные сведения см. в разделе [Настройка OpenVPN для VPN-шлюза Azure](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Дальнейшие шаги

Если вы хотите, чтобы VPN-клиенты могли получать доступ к ресурсам в другой виртуальной сети, следуйте инструкциям в статье " [Виртуальная](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) сеть-vnet", чтобы настроить подключение между виртуальными сетями. Обязательно включите протокол BGP на шлюзах и соединениях, иначе трафик не будет считываться.

**"Опенвпн" является товарным знаком Опенвпн Inc.**
