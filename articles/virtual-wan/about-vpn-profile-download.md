---
title: 'Виртуальная глобальная сеть Azure: профили VPN-клиента пользователя'
description: Описывается, как использовать файл профиля клиента.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: c64e7988094612077131029547682c7ae3d25c98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753149"
---
# <a name="working-with-user-vpn-client-profiles"></a>Работа с профилями VPN-клиента пользователя

Скачанный файл профиля содержит сведения, необходимые для настройки VPN-подключения. Эта статья поможет вам получить и понять сведения, необходимые для профиля клиента VPN пользователя.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Папка **OpenVPN** содержит профиль *ovpn*, который необходимо изменить, чтобы добавить ключ и сертификат. Дополнительные сведения см. в разделе [Configure опенвпн Clients](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о VPN-подключении пользователей виртуальной глобальной сети см. [в статье Создание VPN-подключения пользователя](virtual-wan-point-to-site-portal.md).
