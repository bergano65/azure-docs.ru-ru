---
title: VPN-шлюз Azure. О профилях VPN-клиента "точка — сеть"
description: Описывается, как использовать файл профиля клиента.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/13/2020
ms.author: cherylmc
ms.openlocfilehash: 4526611fa8b80a7b97ee7317f5e285c50f05b0de
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650653"
---
# <a name="about-p2s-vpn-client-profiles"></a>О профилях VPN-клиента "точка — сеть"

Скачанный файл профиля содержит сведения, необходимые для настройки VPN-подключения. Эта статья поможет вам получить и понять сведения, необходимые для профиля VPN-клиента.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Папка **OpenVPN** содержит профиль *ovpn*, который необходимо изменить, чтобы добавить ключ и сертификат. Дополнительные сведения см. в статье [Настройка клиентов OpenVPN для VPN-шлюза Azure](vpn-gateway-howto-openvpn-clients.md#windows). Если на VPN-шлюзе выбрана аутентификация Azure AD, то эта папка отсутствует в ZIP-файле. В этом случае перейдите в папку AzureVPN и найдите файл azurevpnconfig.xml.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [подключениях "точка — сеть"](point-to-site-about.md).
