---
title: Средства управления безопасностью для VPN-шлюза Azure
description: Контрольный список средств управления безопасностью для оценки VPN-шлюза Azure
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 2c32f46ca85007608b5e17f2bf77b0a8f0fb8397
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75862753"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Средства управления безопасностью для VPN-шлюза Azure

В этой статье описываются элементы управления безопасностью, встроенные в VPN-шлюз Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Сеть

| Управление безопасностью | Да/нет | Примечания |
|---|---|--|
| Поддержка конечных точек службы| Н/Д | |
| Поддержка внедрения виртуальной сети| Н/Д | |
| Поддержка сетевой изоляции и брандмауэров| Да | VPN-шлюзы — это выделенные экземпляры виртуальных машин для каждой виртуальной сети клиента.  |
| Поддержка принудительного туннелирования| Да |  |

## <a name="monitoring--logging"></a>Мониторинг & ведения журнала

| Управление безопасностью | Да/нет | Примечания|
|---|---|--|
| Поддержка мониторинга Azure (log Analytics, App Insights и т. д.)| Да | См. раздел [Azure Monitor журналов диагностики/оповещений](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor метрики/оповещение](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Ведение журнала и аудит в плоскости управления и управления| Да | Azure Resource Manager журнал действий. |
| Ведение журнала и аудит в плоскости данных | Да | [Azure Monitor журналы диагностики](../azure-resource-manager/resource-group-audit.md) для ведения журнала и аудита VPN-подключений. |

## <a name="identity"></a>Удостоверение

| Управление безопасностью | Да/нет | Примечания|
|---|---|--|
| Проверка подлинности| Да | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) для управления службой и настройки VPN-шлюза Azure. |
| Авторизация| Да | Поддержка авторизации через [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Защита данных

| Управление безопасностью | Да/нет | Примечания |
|---|---|--|
| Шифрование неактивных на стороне сервера: ключи, управляемые корпорацией Майкрософт | Н/Д | VPN-шлюз, транзитный данные клиента, не хранит данные клиента |
| Шифрование при передаче (например, шифрование ExpressRoute, Шифрование виртуальной сети и шифрование виртуальной сети)| Да | VPN-шлюз шифрует клиентские пакеты между VPN-шлюзами Azure и локальными VPN-устройствами (S2S) или VPN-клиентами (P2S). VPN-шлюзы также поддерживают шифрование между виртуальными сетями. |
| Шифрование неактивных на стороне сервера: ключи, управляемые клиентом (BYOK) | Нет | Указанные клиентом общие ключи шифруются при хранении; но еще не интегрирована с CMK. |
| Шифрование на уровне столбцов (службы данных Azure)| Н/Д | |
| Вызовы API в зашифрованном виде| Да | С помощью [Azure Resource Manager](../azure-resource-manager/index.yml) и HTTPS  |

## <a name="configuration-management"></a>Управление конфигурацией

| Управление безопасностью | Да/нет | Примечания|
|---|---|--|
| Поддержка управления конфигурацией (управление версиями конфигураций и т. д.)| Да | Для операций управления состояние конфигурации VPN-шлюза Azure можно экспортировать как шаблон Azure Resource Manager и с течением времени с управлением версиями. | 

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [встроенных средствах управления безопасностью в службах Azure](../security/fundamentals/security-controls.md).