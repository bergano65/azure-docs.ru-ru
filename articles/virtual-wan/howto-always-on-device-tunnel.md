---
title: Настройка постоянного VPN-туннеля
titleSuffix: Azure Virtual WAN
description: Действия по настройке туннеля VPN-устройства Always On для виртуальной глобальной сети
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/22/2020
ms.author: cherylmc
ms.openlocfilehash: 7adeb9682336b19cc87d8c73a7f9ad8bda5e7828
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564069"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Настройка туннеля VPN-устройства Always On для виртуальной глобальной сети

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Предварительные условия

Необходимо создать конфигурацию типа "точка — сеть" и изменить назначение виртуального концентратора. Инструкции см. в следующих разделах:

* [Создание конфигурации P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Создание концентратора с помощью шлюза P2S](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>Настройка туннеля устройства

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Удаление профиля

Чтобы удалить профиль, выполните следующую команду:

![Очистка](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о виртуальной глобальной сети см. в статье, содержащей [Часто задаваемые вопросы](virtual-wan-faq.md) о ней.