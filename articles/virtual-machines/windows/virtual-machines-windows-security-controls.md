---
title: Средства управления безопасностью для Azure Виртуальные машины Windows
description: Контрольный список средств управления безопасностью для оценки Виртуальные машины Windows Azure
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: f105eac0f6f21ea3358340a4e2aaec7d1f1a95ad
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190559"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Элементы управления безопасностью для Виртуальные машины Windows

В этой статье описываются элементы управления безопасностью, встроенные в Виртуальные машины Windows.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Сеть

| Управление безопасностью | Да/нет | Примечания |
|---|---|--|
| Поддержка конечных точек службы| Да | |
| Поддержка внедрения виртуальной сети| Да | |
| Поддержка сетевой изоляции и брандмауэров| Да |  |
| Поддержка принудительного туннелирования| Да | См. раздел [Настройка принудительного туннелирования с помощью модели развертывания Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Мониторинг & ведения журнала

| Управление безопасностью | Да/нет | Примечания|
|---|---|--|
| Поддержка мониторинга Azure (log Analytics, App Insights и т. д.)| Да | [Мониторинг и обновление виртуальной машины Windows в Azure](tutorial-monitoring.md). |
| Ведение журнала и аудит в плоскости управления и управления| Да |  |
| Ведение журнала и аудит в плоскости данных | Нет |  |

## <a name="identity"></a>Тождество

| Управление безопасностью | Да/нет | Примечания|
|---|---|--|
| Аутентификация| Да |  |
| Авторизация| Да |  |

## <a name="data-protection"></a>Защита данных

| Управление безопасностью | Да/нет | Примечания |
|---|---|--|
| Шифрование неактивных на стороне сервера: ключи, управляемые корпорацией Майкрософт | Да | См. раздел [шифрование виртуальных дисков на виртуальной машине Windows](/azure/virtual-machines/windows/encrypt-disks). |
| Шифрование при передаче (например, шифрование ExpressRoute, Шифрование виртуальной сети и шифрование виртуальной сети)| Да | Виртуальные машины Azure поддерживают шифрование [ExpressRoute](/azure/expressroute) и виртуальной сети. См. раздел [Шифрование транзитного пути в виртуальных машинах](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Шифрование неактивных на стороне сервера: ключи, управляемые клиентом (BYOK) | Да | Ключи, управляемые клиентом, являются поддерживаемым сценарием шифрования Azure. см. раздел [Общие сведения о шифровании Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Шифрование на уровне столбцов (службы данных Azure)| Н/Д | |
| Вызовы API в зашифрованном виде| Да | Через HTTPS и TLS. |



## <a name="configuration-management"></a>Управление конфигурацией

| Управление безопасностью | Да/нет | Примечания|
|---|---|--|
| Поддержка управления конфигурацией (управление версиями конфигураций и т. д.)| Да |  | 

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о [встроенных средствах управления безопасностью в службах Azure](../../security/fundamentals/security-controls.md).
