---
title: Исходящий трафик и конечные точки в Azure Digital Twins | Документация Майкрософт
description: Руководство по созданию конечных точек с помощью Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: c917fab84448684cf29af162ec0781d764605f71
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323969"
---
# <a name="egress-and-endpoints"></a>Исходящий трафик и конечные точки

Azure Digital Twins поддерживает концепцию _конечных точек_, где каждая конечная точка представляет брокер событий или сообщений в пользовательской подписке Azure. События и сообщения могут отправляться в **концентратор событий**, **Сетку событий** и в **разделы служебной шины**.

События будут отправляться в конечные точки в соответствии с предопределенными параметрами маршрутизации. Пользователь может указать, в какую конечную точку будет отправлено событие: `TopologyOperation`, `UdfCustom`, `SensorChange`, `SpaceChange` или `DeviceMessage`.

Общие сведения о маршрутизации и типах событий см. в статье [Маршрутизация событий и сообщений](concepts-events-routing.md).

## <a name="event-types-description"></a>Описание типов событий

Ниже приведены форматы событий для каждого типа.

- `TopologyOperation`

  Применяется к изменениям графа. Свойство `subject` указывает тип затронутого объекта. Типы объектов, которые могут вызывать это событие: `Device, DeviceBlobMetadata`, `DeviceExtendedProperty`, `ExtendedPropertyKey`, `ExtendedType`, `KeyStore`, `Report`, `RoleDefinition`, `Sensor`, `SensorBlobMetadata`, `SensorExtendedProperty`, `Space`, `SpaceBlobMetadata`, `SpaceExtendedProperty`, `SpaceResource`, `SpaceRoleAssignment`, `System`, `User`, `UserBlobMetadata`, `UserExtendedProperty`.

  Пример:

  ```JSON
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "subject": "ExtendedPropertyKey",
    "data": {
      "SpacesToNotify": [
        "3a16d146-ca39-49ee-b803-17a18a12ba36"
      ],
      "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
      "AccessType": "Create"
    },
    "eventType": "TopologyOperation",
    "eventTime": "2018-04-17T17:41:54.9400177Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Имя настраиваемого атрибута | Заменить на |
    | --- | --- |
    | `yourTopicName` | Имя настраиваемого раздела |

- `UdfCustom`

  Событие, отправленное определяемой пользователем функцией (UDF). Обратите внимание, что это событие должно быть отправлено из определяемой пользователем функции.

  Пример:

  ```JSON
  {
    "id": "568fd394-380b-46fa-925a-ebb96f658cce",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "ResourceType": "Space",
      "Payload": "\"Room is not available or air quality is poor\"",
      "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
    },
    "eventType": "UdfCustom",
    "eventTime": "2018-10-02T06:50:15.198Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Имя настраиваемого атрибута | Заменить на |
    | --- | --- |
    | `yourTopicName` | Имя настраиваемого раздела |

- `SensorChange`

  Обновление состояния датчика на основе изменений телеметрии.

  Пример:

  ```JSON
  {
    "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "subject": "SensorChange",
    "data": {
      "Type": "Classic",
      "DataType": "Motion",
      "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
      "Value": "False",
      "PreviousValue": "True",
      "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
      "MessageType": "sensor",
      "Properties": {
        "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
        "ms-activity-id": "ct22YwXEGJ5u.605.0"
      }
    },
    "eventType": "SensorChange",
    "eventTime": "2018-04-17T17:46:18.5452993Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Имя настраиваемого атрибута | Заменить на |
    | --- | --- |
    | `yourTopicName` | Имя настраиваемого раздела |

- `SpaceChange`

  Обновление состояния пространства на основе изменений телеметрии.

  Пример:

  ```JSON
  {
    "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
    "subject": "SpaceChange",
    "data": {
      "Type": null,
      "DataType": "AvailableAndFresh",
      "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "Value": "Room is not available or air quality is poor",
      "PreviousValue": null,
      "RawData": null,
      "transactionId": null,
      "EventTimestamp": null,
      "MessageType": null,
      "Properties": null,
      "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
    },
    "eventType": "SpaceChange",
    "eventTime": "2018-10-02T06:50:20.128Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Имя настраиваемого атрибута | Заменить на |
    | --- | --- |
    | `yourTopicName` | Имя настраиваемого раздела |

- `DeviceMessage`

  Позволяет указать подключение `EventHub`, к которому можно направить необработанные события телеметрии из Azure Digital Twins.

> [!NOTE]
> - `DeviceMessage` можно объединить только с `EventHub`. Вы не сможете объединить `DeviceMessage` с другими типами событий.
> - Вы сможете указать только одну конечную точку комбинации типа `EventHub`/`DeviceMessage`.

## <a name="configuring-endpoints"></a>Настройка конечных точек

Управление конечными точками выполняется через API конечных точек. Ниже приведены некоторые примеры настройки различных поддерживаемых конечных точек. Учитывайте массив типов событий, так как он определяет маршрутизацию для конечной точки.

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- Маршрут к типам событий **служебной шины**: `SensorChange`, `SpaceChange`, `TopologyOperation`

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourTopicName"
  }
  ```

    | Имя настраиваемого атрибута | Заменить на |
    | --- | --- |
    | `yourNamespace` | Пространство имен конечной точки |
    | `yourPrimaryKey` | Первичная строка подключения, которая используется для аутентификации |
    | `yourSecondaryKey` | Вторичная строка подключения, которая используется для аутентификации |
    | `yourTopicName` | Имя настраиваемого раздела |

- Маршрут к типам событий **Сетки событий**: `SensorChange`, `SpaceChange`, `TopologyOperation`

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "yourPrimaryKey",
    "secondaryConnectionString": "yourSecondaryKey",
    "path": "yourTopicName.westus-1.eventgrid.azure.net"
  }
  ```

    | Имя настраиваемого атрибута | Заменить на |
    | --- | --- |
    | `yourPrimaryKey` | Первичная строка подключения, которая используется для аутентификации|
    | `yourSecondaryKey` | Вторичная строка подключения, которая используется для аутентификации |
    | `yourTopicName` | Имя настраиваемого раздела |

- Маршрут к типам событий **концентратора событий**: `SensorChange`, `SpaceChange`, `TopologyOperation`

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourEventHubName"
  }
  ```

    | Имя настраиваемого атрибута | Заменить на |
    | --- | --- |
    | `yourNamespace` | Пространство имен конечной точки |
    | `yourPrimaryKey` | Первичная строка подключения, которая используется для аутентификации |
    | `yourSecondaryKey` | Вторичная строка подключения, которая используется для аутентификации |
    | `yourEventHubName` | Имя концентратора событий |

- Маршрут к типам событий **концентратора событий** `DeviceMessage`. Обратите внимание, что необходимо обязательно включить _EntityPath_ в `connectionString`.

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey;EntityPath=yourEventHubName",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey;EntityPath=yourEventHubName",
    "path": "yourEventHubName"
  }
  ```

    | Имя настраиваемого атрибута | Заменить на |
    | --- | --- |
    | `yourNamespace` | Пространство имен конечной точки |
    | `yourPrimaryKey` | Первичная строка подключения, которая используется для аутентификации |
    | `yourSecondaryKey` | Вторичная строка подключения, которая используется для аутентификации |
    | `yourEventHubName` | Имя концентратора событий |

> [!NOTE]
> При создании конечной точки для начала приема событий может потребоваться от 5 до 10 минут.

## <a name="primary-and-secondary-connection-keys"></a>Первичный и вторичный ключи подключения

Если первичный ключ подключения не авторизован, система автоматически пытается использовать вторичный. Это обеспечивает резервное копирование и позволяет корректно аутентифицировать и обновить первичный ключ через API конечных точек.

Если первичный и вторичный ключи подключения не авторизованы, система перейдет в экспоненциальную задержку со временем ожидания до 30 минут. События будут удаляться при каждом активированном времени ожидания задержки.

Каждый раз, когда система находится в состоянии ожидания задержки, может потребоваться до 30 минут на обновление ключей подключения через API конечных точек.

## <a name="unreachable-endpoints"></a>Недоступные конечные точки

Если конечная точка недоступна, система перейдет в экспоненциальную задержку со временем ожидания до 30 минут. События будут удаляться при каждом активированном времени ожидания задержки.

## <a name="next-steps"></a>Дополнительная информация

Сведения об использовании Swagger Azure Digital Twins см. в [этой статье](how-to-use-swagger.md).

Дополнительные сведения о маршрутизации событий и сообщений в Azure Digital Twins см. в [этой статье](concepts-events-routing.md).
