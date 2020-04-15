---
title: Доступ к рекомендациям безопасности & рекомендаций
description: Узнайте о том, как получить доступ к данным оповещения и рекомендаций безопасности при использовании Центра безопасности Azure для IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: bbea0accc79cafb6fea3f1438a71250dc02f4d62
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311002"
---
# <a name="access-your-security-data"></a>Доступ к данным безопасности

Центр безопасности Azure для IoT хранит оповещения, рекомендации и необработанные данные безопасности (если вы решите сохранить их) в рабочем пространстве журнала Analytics.

## <a name="log-analytics"></a>Log Analytics

Для настройки рабочего пространства Log Analytics используется:

1. Откройте Центр Интернета вещей.
1. Нажмите на лезвие **обзора** под разделом **безопасности**
1. Нажмите **Настройки**и измените конфигурацию рабочего пространства Log Analytics.

Чтобы получить доступ к оповещениям и рекомендациям в рабочем пространстве журнала Analytics после настройки:

1. Выберите оповещение или рекомендацию в Центре безопасности Azure для IoT.
1. Нажмите **дальнейшее исследование,** затем нажмите, **чтобы увидеть, какие устройства имеют это предупреждение нажмите здесь и просмотреть столбец DeviceId**.

Подробную информацию о данных запросов из журналов Analytics можно узнать в [журнале «Начало с запросами» в журнале Analytics.](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)

## <a name="security-alerts"></a>Оповещения безопасности

Оповещения о безопасности хранятся в таблице _AzureSecurityOfThings.SecurityAlert_ в рабочем пространстве журнала Analytics, настроенном для решения IoT Центра безопасности Azure.

Мы предоставили ряд полезных запросов, которые помогут вам начать изучать оповещения о безопасности.

### <a name="sample-records"></a>Образцы записей

Выберите несколько случайных записей

```
// Select a few random records
//
SecurityAlert
| project
    TimeGenerated,
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Описание                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Высокий          | Нападение грубой силы удалось           | Нападение грубой силы на устройство было успешным        |    "Полный исходный\"адрес": "Я 10.165.12.18:\""З",\"\""Имена пользователей": "Я", "Deviceid": "IoT-Устройство-Linux"                                                                       |
| 2018-11-19T12:40:31.000 | /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Высокий          | Успешный локальный вход на устройство      | Обнаружен успешный локальный вход на устройство     | "Удаленный адрес": "?", "Удаленный порт": "", "Местный порт": "", "Логин Шелл": "/бин/су", "Логин Процесс Id": "28207", "Имя пользователя": "нападающий", "DeviceId": "IoT-Device-Linux" |
| 2018-11-19T12:40:31.000 | /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Высокий          | Неудачная попытка локального входа на устройстве  | Обнаружена неудачная попытка локального входа в устройство |    "Удаленный адрес": "?", "Удаленный порт": "", "Местный порт": "", "Логин Шелл": "/бин/су", "Логин Процесс Ид": "22644", "Имя пользователя": "нападающий", "DeviceId": "IoT-Device-Linux" |

### <a name="device-summary"></a>Резюме устройства

Получите количество различных предупреждений безопасности, обнаруженных на прошлой неделе, сгруппированных IoT Концентратор, устройство, серьезность оповещения, тип оповещения.

```
// Get the number of distinct security alerts detected in the last week, grouped by
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Высокий          | Нападение грубой силы удалось           | 9   |
| /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Средний        | Неудачная попытка локального входа на устройстве  | 242 |
| /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Высокий          | Успешный локальный вход на устройство      | 31  |
| /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Средний        | Крипто Монета Майнер                     | 4   |

### <a name="iot-hub-summary"></a>Резюме концентратора IoT

Выберите ряд различных устройств, которые были оповещения на прошлой неделе, по IoT концентратор, серьезность оповещения, тип оповещения

```
// Select number of distinct devices which had alerts in the last week, by
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId,
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | Высокий          | Нападение грубой силы удалось           | 1          |
| /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | Средний        | Неудачная попытка локального входа на устройстве  | 1          |
| /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | Высокий          | Успешный локальный вход на устройство      | 1          |
| /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | Средний        | Крипто Монета Майнер                     | 1          |

## <a name="security-recommendations"></a>Рекомендации по обеспечению безопасности

Рекомендации по безопасности хранятся в таблице _AzureSecurityOfThings.Security Recommendation_ в рабочей области журнала Analytics, настроенной для решения IoT центра безопасности Azure.

Мы предоставили ряд полезных запросов, которые помогут вам начать изучение рекомендаций по безопасности.

### <a name="sample-records"></a>Образцы записей

Выберите несколько случайных записей

```
// Select a few random records
//
SecurityRecommendation
| project
    TimeGenerated,
    IoTHubId=AssessedResourceId,
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```

| TimeGenerated | IoTHubId | deviceId | РекомендацияSeverity | РекомендацияГосударство | РекомендацияDisplayName | Описание | РекомендацииAdditionalеДанные |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Средний | Активна | Найдено правило разрешительного брандмауэра в цепочке ввода | Установлено правило в брандмауэре, содержащее разрешительный шаблон для широкого круга IP-адресов или портов | «Правила»» —\"\"SourceАдрес\"\"\":\",\"\"\"ИсточникПорт\"\"\":\",\"\"НазначениеАдрес :\", DestinationPort : 1337 |
| 2019-03-22T10:50:27.237 | /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Средний | Активна | Найдено правило разрешительного брандмауэра в цепочке ввода | Установлено правило в брандмауэре, содержащее разрешительный шаблон для широкого круга IP-адресов или портов | «Правила»» —\"\"SourceАдрес\"\"\":\",\"\"\"ИсточникПорт\"\"\":\",\"\"НазначениеАдрес :\", DestinationPort : 1337 |

### <a name="device-summary"></a>Резюме устройства

Получите количество четких рекомендаций по безопасности, сгруппированных концентратором IoT, устройством, серьезностью рекомендаций и типом.

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | deviceId      | РекомендацияSeverity | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Высокий          | 2   |
| /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Средний        | 1 |
| /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Высокий          | 1  |
| /подписка/<subscription_id>/ресурсгруппы/<resource_group>/провайдеры/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Средний        | 4   |

## <a name="next-steps"></a>Следующие шаги

- Прочтите Обзор обзора Системы безопасности Azure для [Обзора](overview.md) IoT
- Узнайте больше о Центре безопасности Azure для [IoT-архитектуры](architecture.md)
- Понимание и изучение [Центра безопасности Azure для оповещений IoT](concept-security-alerts.md)
- Понять и изучить [Центр безопасности Azure для рекомендаций IoT](concept-recommendations.md)
