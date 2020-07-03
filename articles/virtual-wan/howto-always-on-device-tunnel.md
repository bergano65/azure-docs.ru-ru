---
title: Настройка постоянного VPN-туннеля
titleSuffix: Azure Virtual WAN
description: Действия по настройке туннеля VPN-устройства Always On для виртуальной глобальной сети
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e3eea639eaa52c07e877476e9215144e916618d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502858"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Настройка туннеля VPN-устройства Always On для виртуальной глобальной сети

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Предварительные условия

Необходимо создать конфигурацию типа "точка — сеть" и изменить назначение виртуального концентратора. Инструкции см. в следующих разделах:

* [Создание конфигурации P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Изменение назначения концентратора](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>Настройка туннеля устройства

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Удаление профиля

Чтобы удалить профиль, выполните следующую команду:

![Очистка](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о виртуальной глобальной сети см. в разделе [часто задаваемые вопросы](virtual-wan-faq.md).