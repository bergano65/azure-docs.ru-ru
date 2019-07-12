---
title: Атрибуты безопасности для Azure Load Balancer
description: Контрольный список атрибутов безопасности для проверки подсистемы балансировки нагрузки
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 1892b918ed35221bc73d5070d5a73ecc359aa4c2
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800085"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Атрибуты безопасности для Azure Load Balancer

В этой статье описываются наиболее распространенные атрибуты безопасности, встроенных в Azure Load Balancer.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>профилактическая;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Шифрование неактивных данных (например, шифрование на стороне сервера, шифрование на стороне сервера с управляемыми клиентом ключами и другие функции шифрования) | Н/Д | |
| Шифрование при обмене данными (например, шифрование ExpressRoute в виртуальной сети шифрования и шифрование между сетями)| Н/Д | |
| Управление ключами шифрования (CMK, BYOK, и т.д.)| Н/Д | |
| Шифрование на уровне столбцов (службы данных Azure)| Н/Д | |
| Вызовы API в зашифрованном виде| Да | С помощью [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="network-segmentation"></a>сегментация сети;

| Атрибут безопасности | Да/нет | Примечания |
|---|---|--|
| Поддержка конечной точки службы| Н/Д | |
| Внедрение поддержки виртуальной сети| Н/Д | . |
| Поддержка сетевой изоляции и Firewalling| Н/Д |  |
| Принудительного туннелирования поддержки| Н/Д | |

## <a name="detection"></a>Обнаружение

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Azure Monitor поддержки (Log analytics, Application insights, и т.д.)| Да | См. в разделе [журналы Azure Monitor для общедоступного Load Balancer уровня Basic](load-balancer-monitor-log.md). |

## <a name="identity-and-access-management"></a>Управление удостоверениями и доступом

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Проверка подлинности| Н/Д |  |
| Authorization| Н/Д |  |


## <a name="audit-trail"></a>журнал аудита;

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Управление и ведение журнала и аудит| Да | См. в разделе [журналы Azure Monitor для общедоступного Load Balancer уровня Basic](load-balancer-monitor-log.md). |
| Ведение журнала данных и аудита | Н/Д |  |

## <a name="configuration-management"></a>Управление конфигурацией

| Атрибут безопасности | Да/нет | Примечания|
|---|---|--|
| Поддержка конфигурации management (Управление версиями конфигурации и т. д.)| Н/Д |  | 
