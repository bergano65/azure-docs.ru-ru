---
title: 'Azure VPN Gateway: О профилях клиентов P2S VPN'
description: Это поможет вам работать с файлом профиля клиента
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528512"
---
# <a name="about-p2s-vpn-client-profiles"></a>О профилях клиентов P2S VPN

Загруженный файл профиля содержит информацию, необходимую для настройки VPN-соединения. Эта статья поможет вам получить и понять информацию, необходимую для профиля клиента VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Папка OpenVPN** содержит профиль *ovpn,* который необходимо модифицировать, чтобы включить ключ и сертификат. Для получения дополнительной [Configure OpenVPN clients for Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows)информации см. Эта папка не будет присутствовать в почтовом файле, если аутентификация Azure AD выбрана на шлюзе VPN. Вместо этого, azurevpnconfig.xml будет в папке AzureVPN.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о пункте к сайту, [см.](point-to-site-about.md)
