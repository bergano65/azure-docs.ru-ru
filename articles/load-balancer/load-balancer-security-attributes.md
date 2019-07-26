---
title: Атрибуты безопасности для Azure Load Balancer
description: Контрольный список атрибутов безопасности для оценки Load Balancer
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 7d40618d5f4fde4a2b3fdfbde8a6de0a049ce3b6
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440875"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Атрибуты безопасности для Azure Load Balancer

В этой статье описываются атрибуты безопасности, встроенные в Azure Load Balancer.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>профилактическая;

| Атрибут безопасности | Да или нет | Примечания |
|---|---|--|
| Шифрование неактивных (таких как шифрование на стороне сервера, шифрование на стороне сервера с помощью управляемых клиентом ключей и другие функции шифрования). | Н/Д | |
| Шифрование при передаче (например, шифрование ExpressRoute, Шифрование виртуальной сети и шифрование виртуальной сети)| Н/Д | |
| Обработка ключа шифрования (CMK, BYOK и т. д.)| Н/Д | |
| Шифрование на уровне столбцов (службы данных Azure)| Н/Д | |
| Вызовы API в зашифрованном виде| Да | Через [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="network-segmentation"></a>сегментация сети;

| Атрибут безопасности | Да или нет | Примечания |
|---|---|--|
| Поддержка конечных точек службы| Н/Д | |
| Поддержка внедрения виртуальной сети| Н/Д | . |
| Поддержка сетевой изоляции и брандмауэров| Н/Д |  |
| Поддержка принудительного туннелирования| Н/Д | |

## <a name="detection"></a>Обнаружение

| Атрибут безопасности | Да или нет | Примечания|
|---|---|--|
| Поддержка мониторинга Azure (log Analytics, App Insights и т. д.)| Да | См. раздел [журналы Azure Monitor для общедоступной базовой Load Balancer](load-balancer-monitor-log.md). |

## <a name="identity-and-access-management"></a>Управление удостоверениями и доступом

| Атрибут безопасности | Да или нет | Примечания|
|---|---|--|
| Проверка подлинности| Н/Д |  |
| Authorization| Н/Д |  |


## <a name="audit-trail"></a>журнал аудита;

| Атрибут безопасности | Да или нет | Примечания|
|---|---|--|
| Ведение журнала и аудит в плоскости управления и управления| Да | См. раздел [журналы Azure Monitor для общедоступной базовой Load Balancer](load-balancer-monitor-log.md). |
| Ведение журнала и аудит в плоскости данных | Н/Д |  |

## <a name="configuration-management"></a>Управление конфигурацией

| Атрибут безопасности | Да или нет | Примечания|
|---|---|--|
| Поддержка управления конфигурацией (управление версиями конфигураций и т. д.)| Н/Д |  | 
