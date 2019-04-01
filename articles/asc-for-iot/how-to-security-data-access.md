---
title: Доступ к данным с помощью центра безопасности Azure для Интернета вещей Preview | Документация Майкрософт
description: Узнайте о способах доступа к данным, оповещения и рекомендации по безопасности при использовании центра безопасности Azure для Интернета вещей.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 510ee9bdefe87c1fab40e58bb715f2a8cce936b7
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758525"
---
# <a name="access-your-security-data"></a>Доступ к данным безопасности 

> [!IMPORTANT]
> Центр безопасности Azure для Интернета вещей находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендуется для производственных рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Центр безопасности Azure (ASC) для Интернета вещей сохраняет оповещения системы безопасности, рекомендаций и необработанной безопасности данных (Если вы решили сохранить) в рабочей области Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Настройка рабочей области Log Analytics, которая используется:

1. Откройте Центр Интернета вещей.
1. Нажмите кнопку **безопасности**
2. Нажмите кнопку **параметры**и измените конфигурацию рабочей области Log Analytics.

Для доступа к рабочей области Log Analytics после настройки:

1. Выберите оповещение или рекомендация в ASC для Интернета вещей. 
2. Нажмите кнопку **Дальнейшее изучение**, затем нажмите кнопку **чтобы увидеть, какие устройства имеют этого предупреждения щелкните здесь и просмотрите столбец DeviceId**.

Дополнительные сведения о запросе данных от Log Analytics, см. в разделе [приступить к работе с запросами в Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Оповещения безопасности

Оповещения системы безопасности, хранятся в _AzureSecurityOfThings.SecurityAlert_ таблицы в рабочей области Log Analytics, настроенной для ASC решения Интернета вещей.

ןנוהמסעאגכום ряд полезных запросов, которые помогут начать изучение оповещений системы безопасности.

### <a name="sample-records"></a>Пример записи

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

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | ОПИСАНИЕ                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018 Г.-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < имя_устройства > | Высокий          | Успешно выполнена Атака методом подбора           | Против атак методом подбора на устройстве успешно.        |    {«Полный исходный адрес»: «[\"10.165.12.18:\"]», «Имена пользователей»: «[\"\"]», «идентификатор» устройства: "IoT-Device-Linux" }                                                                       |
| 2018 Г.-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < имя_устройства > | Высокий          | Успешно локальное имя входа на устройстве      | Обнаружено успешное локальное имя входа на устройство     | {«Удаленный адрес»: «?», «Удаленный порт»: «», «Локальный порт»: «», «Оболочка входа»: «/ bin/su», «Идентификатор процесса входа в систему»: «28207», «имя пользователя»: «злоумышленник», «идентификатор» устройства: "IoT-Device-Linux" } |
| 2018 Г.-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < имя_устройства > | Высокий          | Сбой попытки локальное имя входа на устройстве  | Обнаружен неудачной локального попытки входа на устройство |  {«Удаленный адрес»: «?», «Удаленный порт»: «», «Локальный порт»: «», «Оболочка входа»: «/ bin/su», «Идентификатор процесса входа в систему»: «22644», «имя пользователя»: «злоумышленник», «идентификатор» устройства: "IoT-Device-Linux" } |

### <a name="device-summary"></a>Сводки по устройству

Выберите количество уникальных безопасности предупреждения, обнаруженные на прошлой неделе службой центра Интернета вещей, устройства, серьезность предупреждения, тип оповещения.

```
// Select number of distinct security alerts detected last week by 
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

| IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Количество |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < имя_устройства > | Высокий          | Успешно выполнена Атака методом подбора           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < имя_устройства > | Средний        | Сбой попытки локальное имя входа на устройстве  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < имя_устройства > | Высокий          | Успешно локальное имя входа на устройстве      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < имя_устройства > | Средний        | Работа средства шифрования монеты                     | 4.   |

### <a name="iot-hub-summary"></a>Сводка центра Интернета вещей

Выберите ряд различных устройств, в которых возникли предупреждения на прошлой неделе, по Интернета вещей, серьезность предупреждения, тип оповещения

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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Высокий          | Успешно выполнена Атака методом подбора           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Средний        | Сбой попытки локальное имя входа на устройстве  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Высокий          | Успешно локальное имя входа на устройстве      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Средний        | Работа средства шифрования монеты                     | 1          |

## <a name="security-recommendations"></a>Рекомендации по обеспечению безопасности

Рекомендации по обеспечению безопасности, хранятся в _AzureSecurityOfThings.SecurityRecommendation_ таблицы в рабочей области Log Analytics, настроенной для ASC решения Интернета вещей.

Мы предоставляем несколько полезных запросов, которые помогут начать изучение рекомендации по обеспечению безопасности.

### <a name="sample-records"></a>Пример записи

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
    
| TimeGenerated | IoTHubId | deviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | ОПИСАНИЕ | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < имя_устройства > | Средний | Активна | Правило брандмауэра широкие права в цепочке входной найдено | Обнаружено правило брандмауэра, которое содержит разрешительный шаблон для широкого диапазона IP-адресов или портов. | {«Правила»: "[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\" \" \"Порт_назначения\":\"1337\"}]»} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < имя_устройства > | Средний | Активна | Правило брандмауэра широкие права в цепочке входной найдено | Обнаружено правило брандмауэра, которое содержит разрешительный шаблон для широкого диапазона IP-адресов или портов. | {«Правила»: "[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\" \" \"Порт_назначения\":\"1337\"}]»} |

### <a name="device-summary"></a>Сводки по устройству

Выберите количество рекомендаций по обеспечению безопасности различных active, центр Интернета вещей, устройства, уровень серьезности рекомендаций и тип.

```
// Select number of distinct active security recommendations by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | deviceId      | RecommendationSeverity | Количество |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < имя_устройства > | Высокий          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < имя_устройства > | Средний        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < имя_устройства > | Высокий          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < имя_устройства > | Средний        | 4.   |


## <a name="next-steps"></a>Дальнейшие действия

- Чтение ASC для Интернета вещей [Обзор](overview.md)
- Дополнительные сведения о ASC для Интернета вещей [архитектуры](architecture.md)
- Изучение вопроса [ASC для Интернета вещей оповещений](concept-security-alerts.md)
- Изучение вопроса [ASC для Интернета вещей рекомендации](concept-recommendations.md)