---
title: VPN-шлюз Azure. О профилях VPN-клиента "точка — сеть"
description: Используйте эту статью, чтобы найти сведения, необходимые для профиля VPN-клиента.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6a09767a7992a5f902adea6f99e937f3fc6fa7fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985922"
---
# <a name="about-p2s-vpn-client-profiles"></a>О профилях VPN-клиента "точка — сеть"

Скачанный файл профиля содержит сведения, необходимые для настройки VPN-подключения. Эта статья поможет вам получить и понять сведения, необходимые для профиля VPN-клиента.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Папка **OpenVPN** содержит профиль *ovpn*, который необходимо изменить, чтобы добавить ключ и сертификат. Дополнительные сведения см. в статье [Настройка клиентов OpenVPN для VPN-шлюза Azure](vpn-gateway-howto-openvpn-clients.md#windows). Если на VPN-шлюзе выбрана аутентификация Azure AD, то эта папка отсутствует в ZIP-файле. В этом случае перейдите в папку AzureVPN и найдите файл azurevpnconfig.xml.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [подключениях "точка — сеть"](point-to-site-about.md).
