---
title: Настройка постоянного VPN-туннеля
titleSuffix: Azure VPN Gateway
description: Узнайте, как использовать шлюзы с Windows 10 Always On для установки и настройки постоянных туннелей устройств в Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: ab951375fdf9c5317e2c3a2f67271666d0987142
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89435853"
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
