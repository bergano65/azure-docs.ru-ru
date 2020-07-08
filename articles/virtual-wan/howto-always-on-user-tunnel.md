---
title: Настройка постоянного туннеля VPN-пользователя
titleSuffix: Azure Virtual WAN
description: В этой статье описывается, как настроить туннель VPN пользователя Always On для виртуальной глобальной сети.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/22/2020
ms.author: cherylmc
ms.openlocfilehash: 03f67053a5a199c8c64efb05d2b6a65ad6707650
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564045"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Настройка туннеля VPN-пользователя Always On для виртуальной глобальной сети

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Предварительные условия

Необходимо создать конфигурацию типа "точка — сеть" и изменить назначение виртуального концентратора. Инструкции см. в следующих разделах:

* [Создание конфигурации P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Создание концентратора с помощью шлюза P2S](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-a-user-tunnel"></a>Настройка пользовательского туннеля

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Удаление профиля

Чтобы удалить профиль, выполните следующие действия.

1. Выполните следующую команду:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Отключите подключение и снимите флажок **подключиться автоматически** .

   ![Очистка](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о виртуальной глобальной сети см. в статье, содержащей [Часто задаваемые вопросы](virtual-wan-faq.md) о ней.
