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
ms.openlocfilehash: c8608a980c405f1f9fdd5aa274a9a21d801a59ed
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886650"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Элементы управления безопасностью для Виртуальные машины Linux

В этой статье описываются элементы управления безопасностью, встроенные в Виртуальные машины Linux.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Network

| Управление безопасностью | Да или нет | Примечания |
|---|---|--|
| Поддержка конечных точек службы| Да | |
| Поддержка внедрения виртуальной сети| Да | |
| Поддержка сетевой изоляции и брандмауэров| Да |  |
| Поддержка принудительного туннелирования| Да | См. раздел [Настройка принудительного туннелирования с помощью модели развертывания Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Мониторинг & ведения журнала

| Управление безопасностью | Да или нет | Примечания|
|---|---|--|
| Поддержка мониторинга Azure (log Analytics, App Insights и т. д.)| Да | См. статью [мониторинг и обновление виртуальной машины Linux в Azure](/azure/virtual-machines/linux/tutorial-monitoring) и [Отслеживание и обновление виртуальной машины Windows в Azure](/azure/virtual-machines/windows/tutorial-monitoring). |
| Ведение журнала и аудит в плоскости управления и управления| Да |  |
| Ведение журнала и аудит в плоскости данных | Нет |  |

## <a name="identity"></a>идентификации

| Управление безопасностью | Да или нет | Примечания|
|---|---|--|
| Проверка подлинности| Да |  |
| Authorization| Да |  |

## <a name="data-protection"></a>Защита данных

| Управление безопасностью | Да или нет | Примечания |
|---|---|--|
| Шифрование неактивных на стороне сервера: Ключи, управляемые корпорацией Майкрософт | Да | Узнайте, [как зашифровать виртуальную машину Linux в Azure](/azure/virtual-machines/linux/encrypt-disks) и зашифровать [Виртуальные диски на виртуальной машине Windows](/azure/virtual-machines/windows/encrypt-disks). |
| Шифрование при передаче (например, шифрование ExpressRoute, Шифрование виртуальной сети и шифрование виртуальной сети)| Да | Виртуальные машины Azure поддерживают шифрование [ExpressRoute](/azure/expressroute) и виртуальной сети. См. раздел [Шифрование транзитного пути в виртуальных машинах](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Шифрование неактивных на стороне сервера: ключи, управляемые клиентом (BYOK) | Да | Ключи, управляемые клиентом, являются поддерживаемым сценарием шифрования Azure. см. раздел [Общие сведения о шифровании Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Шифрование на уровне столбцов (службы данных Azure)| Н/Д | |
| Вызовы API в зашифрованном виде| Да | Через HTTPS и SSL. |

## <a name="configuration-management"></a>Управление конфигурацией

| Управление безопасностью | Да или нет | Примечания|
|---|---|--|
| Поддержка управления конфигурацией (управление версиями конфигураций и т. д.)| Да |  | 

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о [встроенных средствах управления безопасностью в службах Azure](../../security/fundamentals/security-controls.md).
