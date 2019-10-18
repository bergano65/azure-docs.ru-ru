---
title: Элементы управления безопасностью для масштабируемых наборов виртуальных машин Azure
description: Контрольный список средств управления безопасностью для оценки масштабируемых наборов виртуальных машин Azure
services: virtual-machine-scale-sets
ms.service: virtual-machine-scale-sets
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 6aee9edaf3c594a2e7083416726da8e57bbf5c6b
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529138"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Элементы управления безопасностью для масштабируемых наборов виртуальных машин Azure

В этой статье описываются элементы управления безопасностью, встроенные в масштабируемые наборы виртуальных машин Azure.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network

| Управление безопасностью | Да/нет | Заметки |
|---|---|--|
| Поддержка конечных точек службы| ДА | |
| Поддержка внедрения виртуальной сети| ДА | |
| Поддержка сетевой изоляции и брандмауэров| ДА |  |
| Поддержка принудительного туннелирования| ДА | См. раздел [Настройка принудительного туннелирования с помощью модели развертывания Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Мониторинг & ведения журнала

| Управление безопасностью | Да/нет | Заметки|
|---|---|--|
| Поддержка мониторинга Azure (log Analytics, App Insights и т. д.)| ДА | См. статью [мониторинг и обновление виртуальной машины Linux в Azure](/azure/virtual-machines/linux/tutorial-monitoring) и [Отслеживание и обновление виртуальной машины Windows в Azure](/azure/virtual-machines/windows/tutorial-monitoring). |
| Ведение журнала и аудит в плоскости управления и управления| ДА |  |
| Ведение журнала и аудит в плоскости данных | Нет |  |

## <a name="identity"></a>Удостоверение

| Управление безопасностью | Да/нет | Заметки|
|---|---|--|
| Authentication| ДА |  |
| Авторизация| ДА |  |

## <a name="data-protection"></a>Защита данных

| Управление безопасностью | Да/нет | Заметки |
|---|---|--|
| Шифрование неактивных на стороне сервера: ключи, управляемые корпорацией Майкрософт | ДА | См. раздел [Шифрование дисков Azure для масштабируемых наборов виртуальных машин](disk-encryption-overview.md). |
| Шифрование при передаче (например, шифрование ExpressRoute, Шифрование виртуальной сети и шифрование виртуальной сети)| ДА | Виртуальные машины Azure поддерживают шифрование [ExpressRoute](/azure/expressroute) и виртуальной сети. См. раздел [Шифрование транзитного пути в виртуальных машинах](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Шифрование неактивных на стороне сервера: ключи, управляемые клиентом (BYOK) | ДА | Ключи, управляемые клиентом, являются поддерживаемым сценарием шифрования Azure. см. раздел [о шифровании дисков Azure для масштабируемых наборов виртуальных машин](disk-encryption-overview.md) .|
| Шифрование на уровне столбцов (службы данных Azure)| Н/Д | |
| Вызовы API в зашифрованном виде| ДА | Через HTTPS и SSL. |

## <a name="configuration-management"></a>Управление конфигурацией

| Управление безопасностью | Да/нет | Заметки|
|---|---|--|
| Поддержка управления конфигурацией (управление версиями конфигураций и т. д.)| ДА |  | 

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [встроенных средствах управления безопасностью в службах Azure](../security/fundamentals/security-controls.md).