---
title: Исходящий трафик и конечные точки в Azure Digital Twins | Документация Майкрософт
description: Руководство по созданию конечных точек с помощью Azure Digital Twins.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: alinast
ms.openlocfilehash: e93811a56f934a95dde45633c4fb64312b3696df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60924811"
---
# <a name="egress-and-endpoints"></a>Исходящий трафик и конечные точки

*Конечные точки* Azure Digital Twins представляют собой брокер сообщений или событий в пользовательской подписке Azure. События и сообщения могут отправляться в Центры событий Azure, Сетку событий Azure и в разделы служебной шины Azure.

События направляются в конечные точки в соответствии с предопределенными настройками маршрутизации. Пользователи указывают *типы событий*, которые будут направляться в каждую конечную точку.

Дополнительные сведения о маршрутизации и типах событий см. в статье [Маршрутизация событий и сообщений](./concepts-events-routing.md).

## <a name="events"></a>События

События отправляются объектами Интернета вещей (например, устройствами и датчиками) для обработки с помощью брокеров сообщений и событий Azure. События определяются с использованием [Схемы событий службы "Сетка событий Azure"](../event-grid/event-schema.md).

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Атрибут | type | ОПИСАНИЕ |
| --- | --- | --- |
| id | string | Уникальный идентификатор события. |
| тема | string | Определенный издателем путь к субъекту событий. |
| данные | object | Данные события, относящиеся к поставщику ресурсов. |
| eventType | string | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | string | Время создания события с учетом времени поставщика в формате UTC. |
| dataVersion | string | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | string | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |
| Раздел | string | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |

Дополнительные сведения о схеме событий службы "Сетка событий Azure":

- См. в статье [Схема событий службы "Сетка событий Azure"](../event-grid/event-schema.md).
- См. [Azure EventGrid Node.js SDK EventGridEvent reference](https://docs.microsoft.com/javascript/api/azure-eventgrid/eventgridevent?view=azure-node-latest) (Интерфейс EventGridEvent).

## <a name="event-types"></a>Типы событий

Типы событий определяют характер события и задаются в поле **eventType**. В следующем списке приведены доступные типы событий:

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

В следующих разделах описываются форматы для каждого из типов событий.

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** применяется к изменениям графа. Свойство **subject** указывает тип затронутого объекта. Это событие могут вызвать следующие типы объектов.

- Устройство
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- KeyStore
- Отчет
- RoleDefinition
- Датчик
- SensorBlobMetadata
- SensorExtendedProperty
- Космос
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- системный;
- Пользователь
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>Пример

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Value | Заменить на |
| --- | --- |
| YOUR_TOPIC_NAME | Имя настраиваемого раздела |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** — это событие, отправленное определяемой пользователем функцией (UDF).
  
> [!IMPORTANT]  
> Это событие нужно явным образом отправлять из определяемой пользователем функции.

#### <a name="example"></a>Пример

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Value | Заменить на |
| --- | --- |
| YOUR_TOPIC_NAME | Имя настраиваемого раздела |

### <a name="sensorchange"></a>SensorChange

**SensorChange** — это обновление состояния датчика на основе изменений телеметрии.

#### <a name="example"></a>Пример

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Value | Заменить на |
| --- | --- |
| YOUR_TOPIC_NAME | Имя настраиваемого раздела |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** — это обновление состояния пространства на основе изменений телеметрии.

#### <a name="example"></a>Пример

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Value | Заменить на |
| --- | --- |
| YOUR_TOPIC_NAME | Имя настраиваемого раздела |

### <a name="devicemessage"></a>DeviceMessage

**DeviceMessage** позволяет указать подключение к **EventHub**, в которое можно направлять необработанные события телеметрии из Azure Digital Twins.

> [!NOTE]
> - **DeviceMessage** комбинируется только с **EventHub**. Нельзя объединять **DeviceMessage** с любым из других типов событий.
> - Вы можете указать только одну конечную точку с комбинацией типов **EventHub** или **DeviceMessage**.

## <a name="configure-endpoints"></a>Настройка конечных точек

Управление конечными точками выполняется через API конечных точек.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

В следующих примерах показано, как настроить поддерживаемые конечные точки.

>[!IMPORTANT]
> Обратите особое внимание на атрибут **eventTypes**. Он определяет, какие типы событий обрабатываются конечной точкой и, таким образом, определяет ее маршрутизацию.

Аутентифицированный запрос HTTP POST

```plaintext
YOUR_MANAGEMENT_API_URL/endpoints
```

- Направляйте в служебную шину события следующих типов: **SensorChange**, **SpaceChange** и **TopologyOperation**.

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | Value | Заменить на |
    | --- | --- |
    | YOUR_NAMESPACE | Пространство имен конечной точки |
    | YOUR_PRIMARY_KEY | Первичная строка подключения, которая используется для аутентификации |
    | YOUR_SECONDARY_KEY | Вторичная строка подключения, которая используется для аутентификации |
    | YOUR_TOPIC_NAME | Имя настраиваемого раздела |

- Направляйте в Сетку событий события следующих типов: **SensorChange**, **SpaceChange** и **TopologyOperation**:

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | Value | Заменить на |
    | --- | --- |
    | YOUR_PRIMARY_KEY | Первичная строка подключения, которая используется для аутентификации|
    | YOUR_SECONDARY_KEY | Вторичная строка подключения, которая используется для аутентификации |
    | YOUR_TOPIC_NAME | Имя настраиваемого раздела |

- Направляйте в Центры событий события следующих типов: **SensorChange**, **SpaceChange** и **TopologyOperation**:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Value | Заменить на |
    | --- | --- |
    | YOUR_NAMESPACE | Пространство имен конечной точки |
    | YOUR_PRIMARY_KEY | Первичная строка подключения, которая используется для аутентификации |
    | YOUR_SECONDARY_KEY | Вторичная строка подключения, которая используется для аутентификации |
    | YOUR_EVENT_HUB_NAME | Имя концентратора событий |

- Направляйте в Центры событий события следующих типов: **DeviceMessage**. Включение `EntityPath` в **connectionString** является обязательным.

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Value | Заменить на |
    | --- | --- |
    | YOUR_NAMESPACE | Пространство имен конечной точки |
    | YOUR_PRIMARY_KEY | Первичная строка подключения, которая используется для аутентификации |
    | YOUR_SECONDARY_KEY | Вторичная строка подключения, которая используется для аутентификации |
    | YOUR_EVENT_HUB_NAME | Имя концентратора событий |

> [!NOTE]  
> После создания конечной точки для начала приема событий может пройти до 5 или даже 10 минут.

## <a name="primary-and-secondary-connection-keys"></a>Первичный и вторичный ключи подключения

Если первичный ключ подключения не авторизован, система автоматически пытается использовать вторичный. Это обеспечивает резервное копирование и позволяет корректно аутентифицировать и обновить первичный ключ через API конечных точек.

Если первичный и вторичный ключи подключения не авторизованы, система перейдет в экспоненциальную задержку со временем ожидания до 30 минут. События будут удаляться при каждом активированном времени ожидания задержки.

Каждый раз, когда система находится в состоянии ожидания задержки, может потребоваться до 30 минут на обновление ключей подключения через API конечных точек.

## <a name="unreachable-endpoints"></a>Недоступные конечные точки

Если конечная точка недоступна, система перейдет в экспоненциальную задержку со временем ожидания до 30 минут. События будут удаляться при каждом активированном времени ожидания задержки.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения см. в статье [Использование Swagger с Digital Twins](how-to-use-swagger.md).

- Дополнительные сведения см. в статье [Маршрутизация событий и сообщений](concepts-events-routing.md).
