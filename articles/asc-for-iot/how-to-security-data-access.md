---
title: Доступ к данным с помощью центра безопасности Azure для IoT | Документация Майкрософт
description: Узнайте, как получить доступ к данным оповещений системы безопасности и рекомендациям при использовании центра безопасности Azure для Интернета вещей.
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
ms.openlocfilehash: 3ddd9b2c8373746a65cd78f0a81b60d097cd9f38
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597180"
---
# <a name="access-your-security-data"></a>Доступ к данным безопасности 

Центр безопасности Azure для Интернета вещей хранит оповещения системы безопасности, рекомендации и необработанные данные безопасности (если вы решили сохранить их) в рабочей области Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Чтобы настроить используемую рабочую область Log Analytics:

1. Откройте Центр Интернета вещей.
1. Щелкните колонку **Обзор** в разделе **Безопасность** .
2. Щелкните **Параметры**и измените конфигурацию рабочей области log Analytics.

Чтобы получить доступ к оповещениям и рекомендациям в рабочей области Log Analytics после настройки:

1. Выберите оповещение или рекомендацию в центре безопасности Azure для Интернета вещей. 
2. Щелкните **дальнейшее исследование**, а затем щелкните, **чтобы узнать, какие устройства имеют это оповещение щелкните здесь и просмотрите столбец DeviceID**.

Дополнительные сведения о запросах данных из Log Analytics см. [в разделе Начало работы с запросами в log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Оповещения безопасности

Оповещения системы безопасности хранятся в таблице _азуресекуритйофсингс. секуритялерт_ в log Analytics рабочей области, настроенной для решения "Центр безопасности Azure для Интернета вещей".

ןנוהמסעאגכום ряд полезных запросов, которые помогут начать изучение оповещений системы безопасности.

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

| TimeGenerated           | иосубид                                                                                                       | Идентификатор устройства      | AlertSeverity | DisplayName                           | Описание                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < DEVICE_NAME > | Высокий          | Успешная атака методом подбора           | Атака методом подбора на устройстве успешно выполнена        |    {"Полный исходный адрес": "[\"10.165.12.18:\"]", "имена пользователей": "[\"\"]", "DeviceID": "IoT-Device-Linux"}                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < DEVICE_NAME > | Высокий          | Успешное локальное имя входа на устройстве      | Обнаружено успешное локальное имя входа на устройство     | {"Удаленный адрес": "?", "удаленный порт": "", "локальный порт": "", "оболочка входа": "/бин/Су", "идентификатор процесса входа": "28207", "имя пользователя": "злоумышленник", "DeviceId": "IoT-Device-Linux"} |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < DEVICE_NAME > | Высокий          | Неудачная попытка локального входа на устройство  | Обнаружена неудачная попытка локального входа на устройство |  {"Удаленный адрес": "?", "удаленный порт": "", "локальный порт": "", "оболочка входа": "/бин/Су", "идентификатор процесса входа": "22644", "имя пользователя": "злоумышленник", "DeviceId": "IoT-Device-Linux"} |

### <a name="device-summary"></a>Сводка по устройствам

Получение числа отдельных оповещений системы безопасности, обнаруженных за последнюю неделю, сгруппированных по центру Интернета вещей, устройству, серьезности оповещения, типу оповещения.

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

| иосубид                                                                                                       | Идентификатор устройства      | AlertSeverity | DisplayName                           | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < DEVICE_NAME > | Высокий          | Успешная атака методом подбора           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < DEVICE_NAME > | Среднее        | Неудачная попытка локального входа на устройство  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < DEVICE_NAME > | Высокий          | Успешное локальное имя входа на устройстве      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < DEVICE_NAME > | Среднее        | Криптография монет Miner                     | 4   |

### <a name="iot-hub-summary"></a>Сводка центра Интернета вещей

Выберите несколько уникальных устройств с предупреждениями за последнюю неделю, центр Интернета вещей, серьезность оповещения, тип оповещения

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

| иосубид                                                                                                       | AlertSeverity | DisplayName                           | кнтдевицес |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Высокий          | Успешная атака методом подбора           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Среднее        | Неудачная попытка локального входа на устройство  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Высокий          | Успешное локальное имя входа на устройстве      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Среднее        | Криптография монет Miner                     | 1          |

## <a name="security-recommendations"></a>Рекомендации по безопасности

Рекомендации по безопасности хранятся в таблице _азуресекуритйофсингс. секуритирекоммендатион_ в log Analytics рабочей области, настроенной для решения "Центр безопасности Azure для Интернета вещей".

Мы предоставили ряд полезных запросов, которые помогут приступить к изучению рекомендаций по безопасности.

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
    
| TimeGenerated | иосубид | Идентификатор устройства | рекоммендатионсеверити | рекоммендатионстате | рекоммендатиондисплайнаме | Описание | рекоммендатионаддитионалдата |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < DEVICE_NAME > | Среднее | Активен | Обнаружено разрешительное правило брандмауэра во входной цепочке | Обнаружено правило брандмауэра, которое содержит разрешительный шаблон для широкого диапазона IP-адресов или портов. | {"Rules": "[\"{\"саурцеаддресс\":\"\",\"саурцепорт\":\",дестинатионаддресс\":\" \"\" ,\"Дестинатионпорт\":\"1337}]"}\" |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < DEVICE_NAME > | Среднее | Активен | Обнаружено разрешительное правило брандмауэра во входной цепочке | Обнаружено правило брандмауэра, которое содержит разрешительный шаблон для широкого диапазона IP-адресов или портов. | {"Rules": "[\"{\"саурцеаддресс\":\"\",\"саурцепорт\":\",дестинатионаддресс\":\" \"\" ,\"Дестинатионпорт\":\"1337}]"}\" |

### <a name="device-summary"></a>Сводка по устройствам

Получите количество уникальных активных рекомендаций по безопасности, сгруппированных по центру Интернета вещей, устройству, серьезности рекомендаций и типу.

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

| иосубид                                                                                                       | Идентификатор устройства      | рекоммендатионсеверити | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < DEVICE_NAME > | Высокий          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < DEVICE_NAME > | Среднее        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < DEVICE_NAME > | Высокий          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < DEVICE_NAME > | Среднее        | 4   |


## <a name="next-steps"></a>Следующие шаги

- Ознакомьтесь с [обзором](overview.md) центра безопасности Azure для IOT
- Узнайте о центре безопасности Azure для [архитектуры](architecture.md) IOT
- Узнайте [об оповещениях центра безопасности Azure для Интернета вещей](concept-security-alerts.md) и изучите их
- Ознакомьтесь с [рекомендациями центра безопасности Azure для IOT](concept-recommendations.md)
