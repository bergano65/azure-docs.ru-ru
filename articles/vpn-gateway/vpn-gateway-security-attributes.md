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
ms.openlocfilehash: c355f70975f441609304a4c9ee2ead75f0e0ce25
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444586"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Атрибуты безопасности для VPN-шлюза Azure

В этой статье описываются атрибуты безопасности, встроенные в VPN-шлюз Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>профилактическая;

| Атрибут безопасности | Да или нет | Примечания |
|---|---|--|
| Шифрование неактивных (таких как шифрование на стороне сервера, шифрование на стороне сервера с помощью управляемых клиентом ключей и другие функции шифрования). | Н/Д | VPN-шлюз, транзитный данные клиента, не хранит данные клиента |
| Шифрование при передаче (например, шифрование ExpressRoute, Шифрование виртуальной сети и шифрование виртуальной сети)| Да | VPN-шлюз шифрует клиентские пакеты между VPN-шлюзами Azure и локальными VPN-устройствами (S2S) или VPN-клиентами (P2S). VPN-шлюзы также поддерживают шифрование между виртуальными сетями. |
| Обработка ключа шифрования (CMK, BYOK и т. д.)| Нет | Указанные клиентом общие ключи шифруются при хранении; но еще не интегрирована с CMK. |
| Шифрование на уровне столбцов (службы данных Azure)| Н/Д | |
| Вызовы API в зашифрованном виде| Да | С помощью [Azure Resource Manager](../azure-resource-manager/index.yml) и HTTPS  |

## <a name="network-segmentation"></a>сегментация сети;

| Атрибут безопасности | Да или нет | Примечания |
|---|---|--|
| Поддержка конечных точек службы| Н/Д | |
| Поддержка внедрения виртуальной сети| Н/Д | . |
| Поддержка сетевой изоляции и брандмауэров| Да | VPN-шлюзы — это выделенные экземпляры виртуальных машин для каждой виртуальной сети клиента.  |
| Поддержка принудительного туннелирования| Да |  |

## <a name="detection"></a>Обнаружение

| Атрибут безопасности | Да или нет | Примечания|
|---|---|--|
| Поддержка мониторинга Azure (log Analytics, App Insights и т. д.)| Да | См. раздел [Azure Monitor журналов диагностики/оповещений](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor метрики/оповещение](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

## <a name="identity-and-access-management"></a>Управление удостоверениями и доступом

| Атрибут безопасности | Да или нет | Примечания|
|---|---|--|
| Проверка подлинности| Да | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) для управления службой и настройки VPN-шлюза Azure. |
| Authorization| Да | Поддержка авторизации через [RBAC](../role-based-access-control/overview.md). |


## <a name="audit-trail"></a>журнал аудита;

| Атрибут безопасности | Да или нет | Примечания|
|---|---|--|
| Ведение журнала и аудит в плоскости управления и управления| Да | Azure Resource Manager журнал действий. |
| Ведение журнала и аудит в плоскости данных | Да | [Azure Monitor журналы диагностики](../azure-resource-manager/resource-group-audit.md) для ведения журнала и аудита VPN-подключений. |

## <a name="configuration-management"></a>Управление конфигурацией

| Атрибут безопасности | Да или нет | Примечания|
|---|---|--|
| Поддержка управления конфигурацией (управление версиями конфигураций и т. д.)| Да | Для операций управления состояние конфигурации VPN-шлюза Azure можно экспортировать как шаблон Azure Resource Manager и с течением времени с управлением версиями. | 