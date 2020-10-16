---
title: Создание и экспорт сертификатов для VPN-подключений пользователей | Виртуальная глобальная сеть Azure
description: Создайте самозаверяющий корневой сертификат, экспортируйте открытый ключ и создайте сертификаты клиентов для VPN-подключений пользователей с помощью PowerShell в Windows 10 или Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 2205f170ee846d4db94db7f524a1c424cfbc8f7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328044"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Создание и экспорт сертификатов для VPN-подключений пользователей

Подключения VPN пользователя (точка-сеть) используют сертификаты для проверки подлинности. Эта статья поможет создать самозаверяющий корневой сертификат, а также сертификаты клиента с помощью PowerShell в Windows 10 или Windows Server 2016.

Выполните описанные действия на компьютере с Windows 10 или Windows Server 2016. Командлеты PowerShell, которые используются для создания сертификатов, являются частью операционной системы и не работают в других версиях Windows. Компьютер с Windows 10 или Windows Server 2016 требуется только для создания сертификатов. После создания сертификатов их можно отправить или установить в любой поддерживаемой клиентской операционной системе.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Дальнейшие шаги

Выполните [шаги виртуальной глобальной сети для VPN-подключения пользователя](virtual-wan-about.md) .
