---
title: Настройка постоянного туннеля VPN-пользователя
titleSuffix: Azure Virtual WAN
description: В этой статье описывается, как настроить туннель VPN пользователя Always On для виртуальной глобальной сети.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: dd5b215b143fbaf487325744a158bb8b05707951
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502871"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Настройка туннеля VPN-пользователя Always On для виртуальной глобальной сети

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Предварительные условия

Необходимо создать конфигурацию типа "точка — сеть" и изменить назначение виртуального концентратора. Инструкции см. в следующих разделах:

* [Создание конфигурации P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Изменение назначения концентратора](virtual-wan-point-to-site-portal.md#edit)

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

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о виртуальной глобальной сети см. в разделе [часто задаваемые вопросы](virtual-wan-faq.md).
