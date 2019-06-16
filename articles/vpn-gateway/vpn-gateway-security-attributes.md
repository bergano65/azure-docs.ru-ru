---
title: Атрибуты безопасности для VPN-шлюза Azure
description: Контрольный список атрибутов безопасности для оценки VPN-шлюза Azure
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e58e7b3f77d9bb673e300aa60ad07ca9dba5153
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083136"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Атрибуты безопасности для VPN-шлюза Azure

В этой статье описываются наиболее распространенные атрибуты безопасности, встроенные в VPN-шлюза Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>профилактическая;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Шифрование неактивных данных (например, шифрование на стороне сервера, шифрование на стороне сервера с управляемыми клиентом ключами и другие функции шифрования) | Н/Д | Данных, передаваемых клиенту VPN шлюза, не хранят данные клиентов |
| Шифрование при обмене данными (например, шифрование ExpressRoute в виртуальной сети шифрования и шифрование между сетями)| Да | VPN-шлюз шифрования пакетов клиента между VPN-шлюзов Azure и клиента на локальном VPN-устройства (S2S) или VPN-клиентов (P2S). VPN-шлюзы также поддерживают шифрование виртуальными сетями. |
| Управление ключами шифрования (CMK, BYOK, и т.д.)| Нет | Указанные клиентом предварительные общие ключи шифруются в неактивном состоянии; но не интегрированы с CMK еще. |
| Шифрование на уровне столбцов (службы данных Azure)| Н/Д | |
| Вызовы API в зашифрованном виде| Да | Через [Azure Resource Manager](../azure-resource-manager/index.yml) и HTTPS  |

## <a name="network-segmentation"></a>сегментация сети;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Поддержка конечной точки службы| Н/Д | |
| Внедрение поддержки виртуальной сети| Н/Д | . |
| Поддержка сетевой изоляции и Firewalling| Да | VPN-шлюзов, выделенных экземпляров виртуальных Машин для каждого клиента виртуальной сети  |
| Принудительного туннелирования поддержки| Да |  |

## <a name="detection"></a>Обнаружение

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Azure Monitor поддержки (Log analytics, Application insights, и т.д.)| Да | См. в разделе [журналы диагностики Azure Monitor или оповещения](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [предупреждение метрик Azure Monitor](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

## <a name="identity-and-access-management"></a>Управление удостоверениями и доступом

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Authentication| Да | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) для управления службой и настройки шлюза Azure VPN. |
| Авторизация| Да | Поддержка авторизации с помощью [RBAC](../role-based-access-control/overview.md). |


## <a name="audit-trail"></a>журнал аудита;

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Управление и ведение журнала и аудит| Да | Журнал действий Azure Resource Manager. |
| Ведение журнала данных и аудита | Да | [Журналы диагностики Azure Monitor](../azure-resource-manager/resource-group-audit.md) для VPN-подключения, ведение журнала и аудит. |

## <a name="configuration-management"></a>Управление конфигурацией

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Поддержка конфигурации management (Управление версиями конфигурации и т. д.)| Да | Для операций управления состояние конфигурации шлюза VPN-ШЛЮЗ Azure можно экспортировать как шаблон Azure Resource Manager и управлять их версиями со временем. | 