---
title: Настройка и Настройка модуля безопасности для Azure RTO
description: Узнайте, как настроить и настроить модуль безопасности для Azure RTO.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: eed0422f574e54ff6d7df486b4929850a26418fa
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514931"
---
# <a name="configure-and-customize-security-module-for-azure-rtos-preview"></a>Настройка и Настройка модуля безопасности для Azure RTO (Предварительная версия)

Используйте следующий файл, чтобы настроить поведение устройства.

## <a name="azure_iot_security_moduleincasc_porth"></a>azure_iot_security_module/инк/asc_port. h

 Поведение каждой конфигурации по умолчанию предоставляется в следующих таблицах: 

### <a name="general"></a>Общие сведения

| Имя | Тип | По умолчанию | Сведения |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | Строка | --- | Уникальный идентификатор устройства  |
| ASC_SECURITY_MODULE_PENDING_TIME  | Number | 300 | Время ожидания модуля безопасности в секундах. Значение, если время превышает изменение состояния для приостановки. |

#### <a name="collection"></a>Коллекция

| Имя | Тип | По умолчанию | Сведения |
| - | - | - | - |
| ASC_HIGH_PRIORITY_INTERVAL | Number | 10 | Интервал групп с высоким приоритетом для собраний в секундах. |
| ASC_MEDIUM_PRIORITY_INTERVAL | Number | 30 | Интервал групп средних приоритетов для собирающих в секундах. |
| ASC_LOW_PRIORITY_INTERVAL | Number | 145 440  | Интервал групп с низким приоритетом для собраний в секундах. |

#### <a name="collector-network-activity"></a>Активность сети сборщика

Чтобы настроить конфигурацию сетевой активности сборщика, используйте следующую команду:

| Имя | Тип | По умолчанию | Сведения |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Логическое значение | false | Фильтрация `TCP` активности сети |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Логическое значение | false | Фильтрация `UDP` событий сетевых операций |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Логическое значение | false | Фильтрация `ICMP` событий сетевых операций |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Логическое значение | Да | Записывать только одноадресные входящие пакеты, если задано значение false, а также широковещательная и многоадресная рассылка |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Number | 64 | Максимальное число событий сети IPv4 для хранения в памяти |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Number | 64  | Максимальное число событий сети IPv6 для хранения в памяти |


## <a name="compile-flags"></a>Флаги компиляции
Флаги компиляции позволяют переопределить предопределенные конфигурации.

### <a name="collectors"></a>Сборщики
| Имя | Тип | По умолчанию | Сведения |
| - | - | - | - |
| collector_heartbeat_enabled | Логическое значение | ON | Включение сборщика пакетов пульса |
| collector_network_activity_enabled | Логическое значение | ON | Включение сборщика активности сети |
| collector_system_information_enabled | Логическое значение | ON | Включение сборщика системных сведений |

## <a name="supported-security-alerts-and-recommendations"></a>Поддерживаемые оповещения и рекомендации по безопасности

Модуль безопасности для Azure RTO поддерживает определенные оповещения и рекомендации по безопасности. Обязательно [Проверьте и настройте соответствующие значения оповещений и рекомендаций](concept-rtos-security-alerts-recommendations.md) для вашей службы.

## <a name="log-analytics-optional"></a>Log Analytics (необязательно)

Включение и настройка Log Analytics могут быть полезными, если вы хотите дополнительно исследовать события и действия устройства, хотя это необязательно и необязательно. Дополнительные сведения см. в статье Настройка и использование [log Analytics с центром безопасности Azure для службы IOT](how-to-security-data-access.md#log-analytics) . 

## <a name="next-steps"></a>Дальнейшие действия

- Обзор и Настройка модуля безопасности для [оповещений и рекомендаций системы безопасности](concept-rtos-security-alerts-recommendations.md) Azure RTO
- При необходимости обратитесь к [модулю безопасности Azure RTO API](azure-rtos-security-module-api.md) .

