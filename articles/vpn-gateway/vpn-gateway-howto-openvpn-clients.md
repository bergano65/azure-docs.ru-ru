---
title: Как настроить клиенты OpenVPN для VPN-шлюза Azure | Документация Майкрософт
description: Узнайте, как настроить Опенвпн для VPN-шлюза Azure для клиентов операционной системы Windows, Linux и Mac.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 92447a541726c1c11b7b10d6d52cf91cfc07f945
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036867"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Настройка клиентов Опенвпн для VPN-шлюза Azure

Эта статья поможет вам настроить **клиенты &reg; протокола опенвпн** .

## <a name="before-you-begin"></a>Подготовка

Убедитесь, что шаги по настройке OpenVPN для вашего VPN-шлюза выполнены. Дополнительные сведения см. в разделе [Настройка OpenVPN для VPN-шлюза Azure](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Дальнейшие действия

Если вы хотите, чтобы VPN-клиенты могли получать доступ к ресурсам в другой виртуальной сети, следуйте инструкциям в статье " [Виртуальная](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) сеть-vnet", чтобы настроить подключение между виртуальными сетями. Обязательно включите протокол BGP на шлюзах и соединениях, иначе трафик не будет считываться.

**"Опенвпн" является товарным знаком Опенвпн Inc.**
