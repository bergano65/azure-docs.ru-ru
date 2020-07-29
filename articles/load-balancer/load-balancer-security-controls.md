---
title: Элементы управления безопасностью для Azure Load Balancer
description: Контрольный список элементов управления безопасностью для оценки Load Balancer
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74214899"
---
# <a name="security-controls-for-azure-load-balancer"></a>Элементы управления безопасностью для Azure Load Balancer

В этой статье описываются элементы управления безопасностью, встроенные в Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Сеть

| Управление безопасностью | Да/нет | Примечания |
|---|---|--|
| Поддержка конечных точек службы| Н/Д | |
| Поддержка внедрения виртуальной сети| Н/Д | |
| Поддержка сетевой изоляции и брандмауэров| Н/Д |  |
| Поддержка принудительного туннелирования| Н/Д | |

## <a name="monitoring--logging"></a>Мониторинг & ведения журнала

| Управление безопасностью | Да/нет | Примечания|
|---|---|--|
| Поддержка мониторинга Azure (log Analytics, App Insights и т. д.)| Да | См. раздел [журналы Azure Monitor для общедоступной базовой Load Balancer](load-balancer-monitor-log.md). |
| Ведение журнала и аудит в плоскости управления и управления| Да | См. раздел [журналы Azure Monitor для общедоступной базовой Load Balancer](load-balancer-monitor-log.md). |
| Ведение журнала и аудит в плоскости данных | Н/Д |  |

## <a name="identity"></a>Идентификация

| Управление безопасностью | Да/нет | Примечания|
|---|---|--|
| Аутентификация| Н/Д |  |
| Авторизация| Н/Д |  |

## <a name="data-protection"></a>Защита данных

| Управление безопасностью | Да/нет | Примечания |
|---|---|--|
| Шифрование неактивных на стороне сервера: ключи, управляемые корпорацией Майкрософт | Н/Д | |
| Шифрование при передаче (например, шифрование ExpressRoute, Шифрование виртуальной сети и шифрование виртуальной сети)| Н/Д | |
| Шифрование неактивных на стороне сервера: ключи, управляемые клиентом (BYOK) | Н/Д | |
| Шифрование на уровне столбцов (службы данных Azure)| Н/Д | |
| Вызовы API в зашифрованном виде| Да | Через [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Управление конфигурацией

| Управление безопасностью | Да/нет | Примечания|
|---|---|--|
| Поддержка управления конфигурацией (управление версиями конфигураций и т. д.)| Недоступно |  | 

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [встроенных средствах управления безопасностью в службах Azure](../security/fundamentals/security-controls.md).