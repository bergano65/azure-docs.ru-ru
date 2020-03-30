---
title: Настройка всегда-на VPN туннель
titleSuffix: Azure Virtual WAN
description: Шаги для настройки Всегда на VPN туннель устройства для виртуального WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e3eea639eaa52c07e877476e9215144e916618d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502858"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Назначайте туннель устройства ВСЕГДА на VPN для виртуального WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Предварительные требования

Необходимо создать конфигурацию от точки к сайту и отсеить виртуальное назначение концентратора. Смотрите следующие разделы для инструкций:

* [Создание конфигурации подключения "точка — сеть"](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Отсечение назначения концентратора](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>Настройка туннеля устройства

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Удаление профиля

Чтобы удалить профиль, запустите следующую команду:

![Очистка](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации [FAQ](virtual-wan-faq.md)о виртуальном WAN, см.