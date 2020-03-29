---
title: Создание и экспорт сертификатов для VPN-соединений пользователя Лазурный виртуальный WAN
description: Создание самозаверяющего корневого сертификата, экспорт открытого ключа и создание сертификатов клиента с помощью PowerShell в Windows 10 или Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: 0303bac88f34c895a4a680cd5bff0e9d1513d2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059911"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Создание и экспорт сертификатов для VPN-соединений пользователя

Пользовательские VPN (от точки к сайту) соединения используют сертификаты для проверки подлинности. Эта статья поможет создать самозаверяющий корневой сертификат, а также сертификаты клиента с помощью PowerShell в Windows 10 или Windows Server 2016.

Выполните описанные действия на компьютере с Windows 10 или Windows Server 2016. Командлеты PowerShell, которые используются для создания сертификатов, являются частью операционной системы и не работают в других версиях Windows. Компьютер с Windows 10 или Windows Server 2016 требуется только для создания сертификатов. После создания сертификатов их можно отправить или установить в любой поддерживаемой клиентской операционной системе.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

Продолжить [виртуальные шаги WAN для vpn-соединения пользователя](virtual-wan-about.md)
