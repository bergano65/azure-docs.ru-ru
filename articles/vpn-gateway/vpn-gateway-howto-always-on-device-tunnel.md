---
title: Настройка постоянного VPN-туннеля
titleSuffix: Azure VPN Gateway
description: Узнайте, как использовать шлюзы с Windows 10 Always On для установки и настройки постоянных туннелей устройств в Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: e64d2223740942fe35ae8a730303bee8893c2489
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88035609"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Настройка туннеля Always On VPN для устройства

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Настройка шлюза

Настройте VPN-шлюз для использования IKEv2 и аутентификацию на основе сертификата с помощью инструкции " [Настройка VPN-подключения типа" точка — сеть](vpn-gateway-howto-point-to-site-resource-manager-portal.md) ".

## <a name="configure-the-device-tunnel"></a>Настройка туннеля устройства

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Удаление профиля

Чтобы удалить профиль, выполните следующую команду:

![Очистка](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Дальнейшие действия

Сведения об устранении неполадок см. [в статье проблемы с подключением "точка — сеть" в Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) .
