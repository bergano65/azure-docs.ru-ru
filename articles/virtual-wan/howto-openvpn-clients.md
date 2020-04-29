---
title: Настройка клиентов Опенвпн для виртуальной глобальной сети Azure
description: Действия по настройке клиентов Опенвпн для виртуальной глобальной сети Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: 133ce2f735344bb636015ec5bc261e661045afc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80108891"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Настройка клиента Опенвпн для виртуальной глобальной сети Azure

Эта статья поможет вам настроить **клиенты &reg; протокола опенвпн** .

## <a name="before-you-begin"></a>Подготовка к работе

Создайте конфигурацию VPN пользователя (подключение типа "точка — сеть"). Убедитесь, что для типа туннеля выбрано значение "Опенвпн". Инструкции см. [в статье Создание конфигурации P2S для виртуальной глобальной сети Azure](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о VPN пользователей (точка-сеть) см. в разделе [Создание VPN-подключений пользователей](virtual-wan-point-to-site-portal.md).

**"Опенвпн" является товарным знаком Опенвпн Inc.**
