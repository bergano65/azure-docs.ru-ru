---
title: Как настроить клиенты OpenVPN для VPN-шлюза Azure | Документация Майкрософт
description: Действия по настройке клиентов OpenVPN для VPN-шлюза Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: bdddf097265e7af688175688b6f3214413a90fdc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984095"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Настройка клиентов Опенвпн для VPN-шлюза Azure

Эта статья поможет вам настроить **клиенты &reg; протокола опенвпн** .

## <a name="before-you-begin"></a>Перед началом

Убедитесь, что шаги по настройке OpenVPN для вашего VPN-шлюза выполнены. Дополнительные сведения см. в разделе [Настройка OpenVPN для VPN-шлюза Azure](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Дальнейшие шаги

Если вы хотите, чтобы VPN-клиенты могли получать доступ к ресурсам в другой виртуальной сети, следуйте инструкциям в статье " [Виртуальная](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) сеть-vnet", чтобы настроить подключение между виртуальными сетями. Обязательно включите протокол BGP на шлюзах и соединениях, иначе трафик не будет считываться.

**"Опенвпн" является товарным знаком Опенвпн Inc.**
