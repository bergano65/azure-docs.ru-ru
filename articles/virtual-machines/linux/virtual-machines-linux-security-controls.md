---
title: Средства управления безопасностью для Azure Виртуальные машины Linux — Linux
description: Контрольный список средств управления безопасностью для оценки Виртуальные машины Linux Azure
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 132f696dd1298384c302eeadc264e857d1edff96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87080069"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Элементы управления безопасностью для Виртуальные машины Linux

В этой статье описываются элементы управления безопасностью, встроенные в Виртуальные машины Linux.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Сеть

| Управление безопасностью | Да/нет | Примечания |
|---|---|--|
| Поддержка конечных точек службы| Да | |
| Поддержка внедрения виртуальной сети| Да | |
| Поддержка сетевой изоляции и брандмауэров| Да |  |
| Поддержка принудительного туннелирования| Да | См. раздел [Настройка принудительного туннелирования с помощью модели развертывания Azure Resource Manager](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="monitoring--logging"></a>Мониторинг & ведения журнала

| Управление безопасностью | Да/нет | Примечания|
|---|---|--|
| Поддержка мониторинга Azure (log Analytics, App Insights и т. д.)| Да | См. статью [мониторинг и обновление виртуальной машины Linux в Azure](./tutorial-monitor.md). |
| Ведение журнала и аудит в плоскости управления и управления| Да |  |
| Ведение журнала и аудит в плоскости данных | Нет |  |

## <a name="identity"></a>Идентификация

| Управление безопасностью | Да/нет | Примечания|
|---|---|--|
| Аутентификация| Да |  |
| Авторизация| Да |  |

## <a name="data-protection"></a>Защита данных

| Управление безопасностью | Да/нет | Примечания |
|---|---|--|
| Шифрование неактивных на стороне сервера: ключи, управляемые корпорацией Майкрософт | Да | См. раздел [Шифрование дисков Azure для виртуальных машин Linux](disk-encryption-overview.md). |
| Шифрование при передаче (например, шифрование ExpressRoute, Шифрование виртуальной сети и шифрование VNet-VNet);| Да | Виртуальные машины Azure поддерживают шифрование [ExpressRoute](../../expressroute/index.yml) и виртуальной сети. См. раздел [Шифрование транзитного пути в виртуальных машинах](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Шифрование неактивных на стороне сервера: ключи, управляемые клиентом (BYOK) | Да | Ключи, управляемые клиентом, являются поддерживаемым сценарием шифрования Azure. см. раздел [Общие сведения о шифровании Azure](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms).|
| Шифрование на уровне столбцов (службы данных Azure)| Недоступно | |
| Вызовы API в зашифрованном виде| Да | Через HTTPS и TLS. |

## <a name="configuration-management"></a>Управление конфигурацией

| Управление безопасностью | Да/нет | Примечания|
|---|---|--|
| Поддержка управления конфигурацией (управление версиями конфигураций и т. д.)| Да |  | 

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [встроенных средствах управления безопасностью в службах Azure](../../security/fundamentals/security-controls.md).
