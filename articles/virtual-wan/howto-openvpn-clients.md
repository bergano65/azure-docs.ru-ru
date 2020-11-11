---
title: Настройка клиентов Опенвпн для виртуальной глобальной сети Azure
description: Действия по настройке клиентов Опенвпн для виртуальной глобальной сети Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 05502b344b1224dff5d12f95b96b05baace98970
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491007"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Настройка клиента Опенвпн для виртуальной глобальной сети Azure

Эта статья поможет вам настроить **клиенты &reg; протокола опенвпн** . Для подключения через протокол Опенвпн можно также использовать VPN-клиент Azure для Windows 10. Дополнительные инструкции можно найти [здесь](openvpn-azure-ad-client.md) .

## <a name="before-you-begin"></a>Подготовка к работе

Создайте конфигурацию VPN пользователя (подключение типа "точка — сеть"). Убедитесь, что для типа туннеля выбрано значение "Опенвпн". Инструкции см. [в статье Создание конфигурации P2S для виртуальной глобальной сети Azure](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о VPN пользователей (точка-сеть) см. в разделе [Создание VPN-подключений пользователей](virtual-wan-point-to-site-portal.md).

**"Опенвпн" является товарным знаком Опенвпн Inc.**
