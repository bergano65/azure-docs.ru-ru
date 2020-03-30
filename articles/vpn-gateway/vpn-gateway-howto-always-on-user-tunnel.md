---
title: Настройка туннеля пользователя Always-On VPN
titleSuffix: Azure VPN Gateway
description: В этой статье описывается, как настроить всегда на VPN пользовательского туннеля для вашего VPN шлюза
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 56934dd13661d8f623e673e2817e87618675c7ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502274"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Настройка туннеля Always On VPN для пользователя

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Настройка шлюза

 Используйте инструкции в [статье Настройка VPN-соединения от точки к сайту](vpn-gateway-howto-point-to-site-resource-manager-portal.md) для настройки VPN шлюза для использования IKEv2 и проверки подлинности на основе сертификатов.

## <a name="configure-a-user-tunnel"></a>Настройка пользовательского туннеля

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Удаление профиля

Чтобы удалить профиль, используйте следующие действия:

1. Выполните следующую команду:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Отключите соединение и автоматически очистите флажок **Connect.**

   ![Очистка](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Дальнейшие действия

Чтобы устранить любые проблемы [Azure point-to-site connection problems](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)с подключением, которые могут возникнуть, см.
