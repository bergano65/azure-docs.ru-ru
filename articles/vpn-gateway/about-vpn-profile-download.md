---
title: VPN-шлюз Azure. О профилях VPN-клиента "точка — сеть"
description: Описывается, как использовать файл профиля клиента.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 326abaa123ede1fbe371c69fcbed47a310b54511
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89424843"
---
# <a name="about-p2s-vpn-client-profiles"></a>О профилях VPN-клиента "точка — сеть"

Скачанный файл профиля содержит сведения, необходимые для настройки VPN-подключения. Эта статья поможет вам получить и понять сведения, необходимые для профиля VPN-клиента.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Папка **OpenVPN** содержит профиль *ovpn*, который необходимо изменить, чтобы добавить ключ и сертификат. Дополнительные сведения см. в статье [Настройка клиентов OpenVPN для VPN-шлюза Azure](vpn-gateway-howto-openvpn-clients.md#windows). Если на VPN-шлюзе выбрана аутентификация Azure AD, то эта папка отсутствует в ZIP-файле. В этом случае перейдите в папку AzureVPN и найдите файл azurevpnconfig.xml.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [подключениях "точка — сеть"](point-to-site-about.md).
