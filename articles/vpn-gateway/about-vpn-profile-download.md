---
title: 'VPN-шлюз Azure: Общие сведения о профилях VPN-клиента P2S'
description: Это поможет вам работать с файлом клиентского профиля.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79528512"
---
# <a name="about-p2s-vpn-client-profiles"></a>О профилях VPN-клиента P2S

Скачанный файл профиля содержит сведения, необходимые для настройки VPN-подключения. Эта статья поможет вам получить и понять сведения, необходимые для профиля клиента VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Папка опенвпн** содержит профиль *ОВПН* , который необходимо изменить, чтобы включить ключ и сертификат. Дополнительные сведения см. в статье [Настройка клиентов опенвпн для VPN-шлюза Azure](vpn-gateway-howto-openvpn-clients.md#windows). Если на VPN-шлюзе выбрана проверка подлинности Azure AD, эта папка не будет находиться в файле zip. Вместо этого азуревпнконфиг. XML будет находиться в папке AzureVPN.

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о "точка — сеть" см. в разделе [сведения о точках на](point-to-site-about.md)узел.
