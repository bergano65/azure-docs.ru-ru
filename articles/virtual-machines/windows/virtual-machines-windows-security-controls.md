---
title: Элементы управления безопасностью для виртуальных машин Windows Azure
description: Контрольный список элементов управления безопасностью для оценки виртуальных машин Windows
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 569c989e8876bb7213d1469c7a70da99aa0b1e9c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455348"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Элементы управления безопасностью для виртуальных машин Windows

В этой статье документируются элементы управления безопасностью, встроенные в виртуальные машины Windows.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Сеть

| Контроль безопасности | Да/нет | Примечания |
|---|---|--|
| Поддержка конечных точек обслуживания| Да | |
| Поддержка инъекций VNet| Да | |
| Поддержка сетевой изоляции и брандмауэра| Да |  |
| Принудительная поддержка туннелирования| Да | [См. Настройка принудительного туннелирования с помощью модели развертывания ресурсов Azure Manager.](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) |

## <a name="monitoring--logging"></a>Мониторинг & лесозаготовки

| Контроль безопасности | Да/нет | Примечания|
|---|---|--|
| Поддержка мониторинга Azure (аналитика журналов, информация о приложениях и т.д.)| Да | [Мониторинг и обновление виртуальной машины Windows в Azure](tutorial-monitoring.md). |
| Управление и управление самолетом лесозаготовки и аудит| Да |  |
| Регистрация и аудит плоскости данных | нет |  |

## <a name="identity"></a>Идентификация

| Контроль безопасности | Да/нет | Примечания|
|---|---|--|
| Аутентификация| Да |  |
| Авторизация| Да |  |

## <a name="data-protection"></a>Защита данных

| Контроль безопасности | Да/нет | Примечания |
|---|---|--|
| Шифрование на стороне сервера в состоянии покоя: клавиши, управляемые Корпорацией Майкрософт | Да | Смотрите [шифрование виртуальных дисков на Windows VM](/azure/virtual-machines/windows/disk-encryption-overview). |
| Шифрование в пути (например, шифрование ExpressRoute, шифрование VNet и шифрование VNet-VNet)| Да | Виртуальные машины Azure поддерживают шифрование [ExpressRoute](/azure/expressroute) и VNet. Смотрите [шифрование в пути в VMs](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Шифрование на стороне сервера в состоянии покоя: ключи, управляемые клиентом (BYOK) | Да | Управляемые клиентами ключи — это поддерживаемый сценарий шифрования Azure; смотрите [обзор шифрования Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Шифрование уровня столбцов (Службы данных Azure)| Недоступно | |
| Вызовы API в зашифрованном виде| Да | Через HTTPS и TLS. |



## <a name="configuration-management"></a>Управление конфигурацией

| Контроль безопасности | Да/нет | Примечания|
|---|---|--|
| Поддержка управления конфигурацией (версия конфигурации и т.д.)| Да |  | 

## <a name="next-steps"></a>Следующие шаги

- Узнайте больше о [встроенных элементах управления безопасностью в службах Azure.](../../security/fundamentals/security-controls.md)
