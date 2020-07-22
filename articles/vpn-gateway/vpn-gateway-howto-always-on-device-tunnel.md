---
title: Настройка постоянного VPN-туннеля
titleSuffix: Azure VPN Gateway
description: Действия по настройке VPN-туннеля Always On для VPN-шлюза
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 6db48928ebac115c42c643d669f6541a3654a53a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84983127"
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

## <a name="next-steps"></a>Дальнейшие шаги

Сведения об устранении неполадок см. [в статье проблемы с подключением "точка — сеть" в Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) .
