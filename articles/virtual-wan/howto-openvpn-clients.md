---
title: Настройка клиентов Опенвпн для виртуальной глобальной сети Azure
description: Действия по настройке клиентов Опенвпн для виртуальной глобальной сети Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: f955b5347bc5a2c0fe1fd8a93e14badab1f354c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753272"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Настройка клиента Опенвпн для виртуальной глобальной сети Azure

Эта статья поможет вам настроить **клиенты &reg; протокола опенвпн** .

## <a name="before-you-begin"></a>Перед началом

Создайте конфигурацию VPN пользователя (подключение типа "точка — сеть"). Убедитесь, что для типа туннеля выбрано значение "Опенвпн". Инструкции см. [в статье Создание конфигурации P2S для виртуальной глобальной сети Azure](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о VPN пользователей (точка-сеть) см. в разделе [Создание VPN-подключений пользователей](virtual-wan-point-to-site-portal.md).

**"Опенвпн" является товарным знаком Опенвпн Inc.**
