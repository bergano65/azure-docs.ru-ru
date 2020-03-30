---
title: Элементы управления безопасностью для VPN шлюза Azure
description: Контрольный список средств контроля безопасности для оценки Azure VPN Gateway
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: cdf616b29a93e786ef26af83b5d3b3541f94d67c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972278"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Элементы управления безопасностью для VPN шлюза Azure

В этой статье документируются элементы управления безопасностью, встроенные в Azure VPN Gateway.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Сеть

| Контроль безопасности | Да/нет | Примечания |
|---|---|--|
| Поддержка конечных точек обслуживания| Недоступно | |
| Поддержка инъекций VNet| Недоступно | |
| Поддержка сетевой изоляции и брандмауэра| Да | VPN шлюзы предназначены VM экземпляры для каждого клиента Виртуальная сеть  |
| Принудительная поддержка туннелирования| Да |  |

## <a name="monitoring--logging"></a>Мониторинг & лесозаготовки

| Контроль безопасности | Да/нет | Примечания|
|---|---|--|
| Поддержка мониторинга Azure (аналитика журналов, информация о приложениях и т.д.)| Да | Смотрите [журналы диагностики мониторинга мониторинга Azure Monitor/alert](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor Metrics/alert.](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)  |
| Управление и управление самолетом лесозаготовки и аудит| Да | Регистрация действий менеджера ресурсов Azure. |
| Регистрация и аудит плоскости данных | Да | [Диагностические журналы Azure Monitor](../azure-resource-manager/management/view-activity-logs.md) для регистрации и аудита VPN-соединений. |

## <a name="identity"></a>идентификации

| Контроль безопасности | Да/нет | Примечания|
|---|---|--|
| Проверка подлинности| Да | [Активный каталог Azure](../active-directory/fundamentals/active-directory-whatis.md) для управления сервисом и настройки VPN шлюза Azure. |
| Авторизация| Да | Авторизация поддержки через [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Защита данных

| Контроль безопасности | Да/нет | Примечания |
|---|---|--|
| Шифрование на стороне сервера в состоянии покоя: клавиши, управляемые Корпорацией Майкрософт | Недоступно | VPN шлюз транзитных данных клиентов, не хранит данные клиентов |
| Шифрование в пути (например, шифрование ExpressRoute, шифрование VNet и шифрование VNet-VNet)| Да | VPN шлюз шифрует пакеты клиентов между шлюзами Azure VPN и клиентами на предпосылке VPN-устройств (S2S) или VPN-клиентами (P2S). VPN шлюзы также поддерживают шифрование VNet-to-VNet. |
| Шифрование на стороне сервера в состоянии покоя: ключи, управляемые клиентом (BYOK) | нет | Заранее общие ключи, указанные клиентом, шифруются в состоянии покоя; но еще не интегрированс с CMK. |
| Шифрование уровня столбцов (Службы данных Azure)| Недоступно | |
| Вызовы API в зашифрованном виде| Да | Через [менеджер ресурсов Azure](../azure-resource-manager/index.yml) и HTTPS  |

## <a name="configuration-management"></a>Управление конфигурацией

| Контроль безопасности | Да/нет | Примечания|
|---|---|--|
| Поддержка управления конфигурацией (версия конфигурации и т.д.)| Да | Для управления состояние конфигурации VPN-шлюза Azure можно экспортировать в качестве шаблона управления ресурсами Azure и версии с течением времени. |

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [встроенных элементах управления безопасностью в службах Azure.](../security/fundamentals/security-controls.md)
