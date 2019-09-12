---
title: Элементы управления безопасностью для Azure Load Balancer
description: Контрольный список элементов управления безопасностью для оценки Load Balancer
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: e0be6635a0916183e1dfe776bef4c547578383dc
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886652"
---
# <a name="security-controls-for-azure-load-balancer"></a>Элементы управления безопасностью для Azure Load Balancer

В этой статье описываются элементы управления безопасностью, встроенные в Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network

| Управление безопасностью | Да или нет | Примечания |
|---|---|--|
| Поддержка конечных точек службы| Н/Д | |
| Поддержка внедрения виртуальной сети| Н/Д | |
| Поддержка сетевой изоляции и брандмауэров| Н/Д |  |
| Поддержка принудительного туннелирования| Н/Д | |

## <a name="monitoring--logging"></a>Мониторинг & ведения журнала

| Управление безопасностью | Да или нет | Примечания|
|---|---|--|
| Поддержка мониторинга Azure (log Analytics, App Insights и т. д.)| Да | См. раздел [журналы Azure Monitor для общедоступной базовой Load Balancer](load-balancer-monitor-log.md). |
| Ведение журнала и аудит в плоскости управления и управления| Да | См. раздел [журналы Azure Monitor для общедоступной базовой Load Balancer](load-balancer-monitor-log.md). |
| Ведение журнала и аудит в плоскости данных | Н/Д |  |

## <a name="identity"></a>идентификации

| Управление безопасностью | Да или нет | Примечания|
|---|---|--|
| Проверка подлинности| Н/Д |  |
| Authorization| Н/Д |  |

## <a name="data-protection"></a>Защита данных

| Управление безопасностью | Да или нет | Примечания |
|---|---|--|
| Шифрование неактивных на стороне сервера: Ключи, управляемые корпорацией Майкрософт | Н/Д | |
| Шифрование при передаче (например, шифрование ExpressRoute, Шифрование виртуальной сети и шифрование виртуальной сети)| Н/Д | |
| Шифрование неактивных на стороне сервера: ключи, управляемые клиентом (BYOK) | Н/Д | |
| Шифрование на уровне столбцов (службы данных Azure)| Н/Д | |
| Вызовы API в зашифрованном виде| Да | Через [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Управление конфигурацией

| Управление безопасностью | Да или нет | Примечания|
|---|---|--|
| Поддержка управления конфигурацией (управление версиями конфигураций и т. д.)| Н/Д |  | 

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о [встроенных средствах управления безопасностью в службах Azure](../security/fundamentals/security-controls.md).