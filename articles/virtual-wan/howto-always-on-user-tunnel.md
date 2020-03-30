---
title: Настройка туннеля пользователя Always-On VPN
titleSuffix: Azure Virtual WAN
description: В этой статье описывается, как настроить всегда на VPN пользовательского туннеля для вашего виртуального WAN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: dd5b215b143fbaf487325744a158bb8b05707951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502871"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Наймите всегда на тоннеле пользователя VPN для виртуального WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Предварительные требования

Необходимо создать конфигурацию от точки к сайту и отсеить виртуальное назначение концентратора. Смотрите следующие разделы для инструкций:

* [Создание конфигурации подключения "точка — сеть"](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Отсечение назначения концентратора](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-a-user-tunnel"></a>Настройка пользовательского туннеля

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Удаление профиля

Чтобы удалить профиль, используйте следующие действия:

1. Выполните следующую команду:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Отключите соединение и автоматически очистите флажок **Connect.**

   ![Очистка](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации [FAQ](virtual-wan-faq.md)о виртуальном WAN, см.
