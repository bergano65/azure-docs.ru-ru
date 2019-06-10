---
title: Схема службы "Сетка событий Azure" для Центра Интернета вещей | Документация Майкрософт
description: Страница со справочными сведениями о формате схемы событий и свойствах Центра Интернета вещей
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: kgremban
ms.openlocfilehash: e770beb0470b54d8e13493bca4790323b2e96ce1
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393189"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Схема событий службы "Сетка событий Azure" для Центра Интернета вещей

В этой статье описаны свойства и схема для событий Центра Интернета вещей Azure. Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md). 

Список примеров сценариев и руководств см. в статье [Источники событий в службе "Сетка событий Azure"](event-sources.md#iot-hub).

## <a name="available-event-types"></a>Доступные типы событий

Центр Интернета вещей выдает следующие типы событий:

| Тип события | Описание |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Публикуется при регистрации устройства в Центре Интернета вещей. |
| Microsoft.Devices.DeviceDeleted | Публикуется при удалении устройства из Центра Интернета вещей. | 
| Microsoft.Devices.DeviceConnected | Публикуется при подключении устройства к Центру Интернета вещей. |
| Microsoft.Devices.DeviceDisconnected | Публикуется при отключении устройства от Центра Интернета вещей. | 
| Microsoft.Devices.DeviceTelemetry | Опубликован, при отправке сообщения телеметрии в центр Интернета вещей. |

Все события устройства, за исключением события телеметрии устройства обычно доступны во всех регионах, поддерживаемых службой "Сетка событий". Событие телеметрии устройства в общедоступной предварительной версии и доступна во всех регионах, за исключением восточной части США, Западная часть США, Западная Европа, [Azure для государственных организаций](/azure-government/documentation-government-welcome.md), [21Vianet в Azure для Китая](/azure/china/china-welcome.md), и [Azure для Германии](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="example-event"></a>Пример события

Структура схемы для событий DeviceConnected и DeviceDisconnected идентична. Этот пример демонстрирует схему события, возникающего при подключении устройства в Центре Интернета вещей:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

Событие DeviceTelemetry возникает в том случае, когда событие телеметрии отправляется в центр Интернета вещей. Ниже приведен образец схемы для данного события.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Структура схемы для событий DeviceCreated и DeviceDeleted идентична. Этот пример демонстрирует схему события, возникающего при регистрации устройства в Центре Интернета вещей:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Свойства события

Все события содержат одинаковые данные верхнего уровня: 

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| id | string | Уникальный идентификатор события. |
| topic | string | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | string | Определенный издателем путь к субъекту событий. |
| eventType | string | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | string | Время создания события с учетом времени поставщика в формате UTC. |
| data | object | Данные событий в Центре Интернета вещей.  |
| dataVersion | string | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | string | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Для всех событий Центра Интернета вещей объект данных содержит следующие свойства:

| Свойство | type | Описание |
| -------- | ---- | ----------- |
| hubName | string | Имя Центра Интернета вещей, в котором было создано или из которого было удалено устройство. |
| deviceId | string | Уникальный идентификатор устройства. Это строка с учетом регистра длиной до 128 знаков, состоящая из букв и цифр в 7-битном формате ASCII, а также следующие специальные символы: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

Для всех издателей событий используется различное содержимое объекта данных. 

Для событий **устройство подключено** и **устройство отключено** Центра Интернета вещей объект данных содержит следующие свойства:

| Свойство | type | Описание |
| -------- | ---- | ----------- |
| moduleId | string | Уникальный идентификатор модуля. Это поле выводится только для устройств модуля. Это строка с учетом регистра длиной до 128 знаков, состоящая из букв и цифр в 7-битном формате ASCII, а также следующие специальные символы: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | object | Сведения о событии состояния подключения устройства.
| sequenceNumber | string | Номер, который помогает указать порядок событий подключения или отключения устройства. У последнего события будет порядковый номер, который выше предыдущего. Это число может изменяться более чем на 1, но строго в сторону увеличения. См. [способы использования порядковых номеров](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Для **данные телеметрии устройства** событий центра Интернета вещей объект данных содержит сообщение device-to-cloud в [формате сообщений центра Интернета вещей](../iot-hub/iot-hub-devguide-messages-construct.md) и имеет следующие свойства:

| Свойство | type | Описание |
| -------- | ---- | ----------- |
| текст | string | Содержимое сообщения с устройства. |
| properties | string | Свойства приложения — это определяемые пользователем строки, которые можно добавить в сообщение. Эти поля необязательные. |
| Свойства системы | string | [Системные свойства](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) помогает определить содержимое и источник сообщений. Сообщение телеметрии устройства должно быть в допустимом формате JSON с contentType значение JSON, а также contentEncoding, значение UTF-8 в системные свойства сообщений. Если это значение не задано, центр Интернета вещей будет записывать сообщения в базовый 64 закодированном формате.  |

Для событий **устройство создано** и **устройство удалено** Центра Интернета вещей объект данных содержит следующие свойства:

| Свойство | type | Описание |
| -------- | ---- | ----------- |
| twin | object | Сведения о двойнике устройства, который является представлением метаданные устройства для приложения в облаке. | 
| deviceID | string | Уникальный идентификатор двойника устройства. | 
| etag | string | Проверяющий элемент управления для обеспечения согласованности обновлений двойника устройства. Для каждого двойника устройства обеспечена уникальность каждого тега etag. |  
| deviceEtag| string | Проверяющий элемент управления для обеспечения согласованности обновлений реестра устройства. Каждый deviceEtag гарантированно будет уникальным для каждого реестра устройства. |
| status | string | Состояние двойника устройства: включен или отключен. | 
| statusUpdateTime | string | Метка времени последнего обновления состояния для двойника устройства в формате ISO8601. |
| connectionState | string | Состояние подключения двойника устройства: подключен или не подключен. | 
| lastActivityTime | string | Метка времени последнего действия в формате ISO8601. | 
| cloudToDeviceMessageCount | целое число | Число сообщений, отправленных из облака на это устройство. | 
| authenticationType | string | Тип аутентификации, используемый для этого устройства: `SAS`, `SelfSigned` или `CertificateAuthority`. |
| x509Thumbprint | string | Отпечаток — это уникальное значение для сертификата x509, которое обычно используется для поиска определенного сертификата в хранилище сертификатов. Отпечаток динамически создается с помощью алгоритма SHA1 и физически не существует в сертификате. | 
| primaryThumbprint | string | Первичный отпечаток для сертификата x509. |
| secondaryThumbprint | string | Вторичный отпечаток для сертификата x509. | 
| version | целое число | Целое число, которое увеличивается на единицу при каждом обновлении двойника устройства. |
| desired | object | Часть свойств, которые могут быть записаны только серверной частью приложения и считаны устройством. | 
| reported | object | Часть свойств, которые могут быть записаны только устройством и считаны серверной частью приложения. |
| lastUpdated | string | Метка времени последнего обновления свойств для двойника устройства в формате ISO8601. | 

## <a name="next-steps"></a>Дальнейшие действия

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о совместной работе Центра Интернета вещей и службы "Сетка событий" см. в статье о [реагировании на события Центра Интернета вещей с использованием службы "Сетка событий" для активации действий](../iot-hub/iot-hub-event-grid.md).