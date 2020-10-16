---
title: Настройка клиентов Опенвпн для виртуальной глобальной сети Azure
description: Действия по настройке клиентов Опенвпн для виртуальной глобальной сети Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 68238a084684aeda557690812403995a4dc1e8c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317673"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Настройка клиента Опенвпн для виртуальной глобальной сети Azure

Эта статья поможет вам настроить **клиенты &reg; протокола опенвпн** .

## <a name="before-you-begin"></a>Перед началом

Создайте конфигурацию VPN пользователя (подключение типа "точка — сеть"). Убедитесь, что для типа туннеля выбрано значение "Опенвпн". Инструкции см. [в статье Создание конфигурации P2S для виртуальной глобальной сети Azure](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о VPN пользователей (точка-сеть) см. в разделе [Создание VPN-подключений пользователей](virtual-wan-point-to-site-portal.md).

**"Опенвпн" является товарным знаком Опенвпн Inc.**
