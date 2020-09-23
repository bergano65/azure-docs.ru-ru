---
title: Настройка и Настройка модуля безопасности для Azure RTO
description: Узнайте, как настроить и настроить модуль безопасности для Azure RTO.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 9106e00fe2146978f97b480e3afd3b7ed58c9130
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937778"
---
# <a name="configure-and-customize-security-module-for-azure-rtos-preview"></a>Настройка и Настройка модуля безопасности для Azure RTO (Предварительная версия)

Используйте следующий файл, чтобы настроить поведение устройства.

## <a name="azure_iot_security_moduleincasc_porth"></a>azure_iot_security_module/инк/asc_port. h

 Поведение каждой конфигурации по умолчанию предоставляется в следующих таблицах: 

### <a name="general"></a>Общие сведения

| Имя | Тип | По умолчанию | Подробнее |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | Тип String | --- | Уникальный идентификатор устройства  |
| ASC_SECURITY_MODULE_PENDING_TIME  | Номер | 300 | Время ожидания модуля безопасности в секундах. Значение, если время превышает изменение состояния для приостановки. |

#### <a name="collection"></a>Collection

| Имя | Тип | По умолчанию | Подробнее |
| - | - | - | - |
| ASC_HIGH_PRIORITY_INTERVAL | Номер | 10 | Интервал групп с высоким приоритетом для собраний в секундах. |
| ASC_MEDIUM_PRIORITY_INTERVAL | Номер | 30 | Интервал групп средних приоритетов для собирающих в секундах. |
| ASC_LOW_PRIORITY_INTERVAL | Номер | 145 440  | Интервал групп с низким приоритетом для собраний в секундах. |

#### <a name="collector-network-activity"></a>Активность сети сборщика

Чтобы настроить конфигурацию сетевой активности сборщика, используйте следующую команду:

| Имя | Тип | По умолчанию | Подробнее |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Логическое значение | false | Фильтрация `TCP` активности сети |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Логическое значение | false | Фильтрация `UDP` событий сетевых операций |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Логическое значение | false | Фильтрация `ICMP` событий сетевых операций |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Логическое значение | Да | Записывать только одноадресные входящие пакеты, если задано значение false, а также широковещательная и многоадресная рассылка |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Номер | 64 | Максимальное число событий сети IPv4 для хранения в памяти |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Номер | 64  | Максимальное число событий сети IPv6 для хранения в памяти |


## <a name="compile-flags"></a>Флаги компиляции
Флаги компиляции позволяют переопределить предопределенные конфигурации.

### <a name="collectors"></a>Сборщики
| Имя | Тип | По умолчанию | Подробнее |
| - | - | - | - |
| collector_heartbeat_enabled | Логическое значение | ON | Включение сборщика пакетов пульса |
| collector_network_activity_enabled | Логическое значение | ON | Включение сборщика активности сети |
| collector_system_information_enabled | Логическое значение | ON | Включение сборщика системных сведений |

## <a name="supported-security-alerts-and-recommendations"></a>Поддерживаемые оповещения и рекомендации по безопасности

Модуль безопасности для Azure RTO поддерживает определенные оповещения и рекомендации по безопасности. Обязательно [Проверьте и настройте соответствующие значения оповещений и рекомендаций](concept-rtos-security-alerts-recommendations.md) для вашей службы.

## <a name="log-analytics-optional"></a>Log Analytics (необязательно)

Включение и настройка Log Analytics могут быть полезными, если вы хотите дополнительно исследовать события и действия устройства, хотя это необязательно и необязательно. Узнайте, как настроить и использовать [log Analytics со службой "защитник для Интернета вещей"](how-to-security-data-access.md#log-analytics) для получения дополнительных сведений. 

## <a name="next-steps"></a>Дальнейшие действия

- Обзор и Настройка модуля безопасности для [оповещений и рекомендаций системы безопасности](concept-rtos-security-alerts-recommendations.md) Azure RTO
- При необходимости обратитесь к [модулю безопасности Azure RTO API](azure-rtos-security-module-api.md) .

