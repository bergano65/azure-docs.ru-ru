---
title: REST API - Azure Event Grid IoT Edge Документы Майкрософт
description: REST API на Event Grid на IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 19f86b1d8233e05844201e1095c1f79324955cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841835"
---
# <a name="rest-api"></a>REST API
В этой статье описаны REST AIS из Azure Event Grid на IoT Edge

## <a name="common-api-behavior"></a>Общее поведение API

### <a name="base-url"></a>Базовый URL-адрес
Event Grid on IoT Edge имеет следующие AIS, выставленные по HTTP (порт 5888) и HTTPS (порт 4438).

* Базовый URL для HTTP:http://eventgridmodule:5888
* Базовый URL для HTTPS:https://eventgridmodule:4438

### <a name="request-query-string"></a>Строка запроса запроса
Все запросы API требуют следующего параметра строки запроса:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Тип содержимого запроса
Все запросы API должны иметь **тип содержимого.**

В случае **EventGridSchema** или **CustomSchema**значение Content-Type может быть одним из следующих значений:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

В случае **CloudEventSchemaV1_0** в структурированном режиме значение Content-Type может быть одним из следующих значений:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

В случае **CloudEventSchemaV1_0** в двоичном режиме, обратитесь к [документации](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) для получения подробной информации.

### <a name="error-response"></a>Сообщение об ошибке
Все AI возвращают ошибку со следующей полезной нагрузкой:

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>Управление темами

### <a name="put-topic-create--update"></a>Поместите тему (создать / обновить)

**Запрос**:``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Полезная нагрузка**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Ответ**: HTTP 200

**Полезная нагрузка**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>Получить тему

**Запрос**:``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Ответ**: HTTP 200

**Полезная нагрузка**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>Получить все темы

**Запрос**:``` GET /topics?api-version=2019-01-01-preview ```

**Ответ**: HTTP 200

**Полезная нагрузка**:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>Удалить тему

**Запрос**:``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Ответ**: HTTP 200, пустая полезная нагрузка

## <a name="manage-event-subscriptions"></a>Управление подписками на события
Образцы в `EndpointType=Webhook;`этом разделе используют . Образцы json `EndpointType=EdgeHub / EndpointType=EventGrid` для находятся в следующем разделе. 

### <a name="put-event-subscription-create--update"></a>Поместите подписку на события (создать / обновить)

**Запрос**:``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Полезная нагрузка**:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**Ответ**: HTTP 200

**Полезная нагрузка**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>Получить подписку на события

**Запрос**:``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Ответ**: HTTP 200

**Полезная нагрузка**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>Получить подписку на мероприятия

**Запрос**:``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Ответ**: HTTP 200

**Полезная нагрузка**:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>Удаление подписки на события

**Запрос**:``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Ответ**: HTTP 200, нет полезной нагрузки


## <a name="publish-events-api"></a>Публикация API событий

### <a name="send-batch-of-events-in-event-grid-schema"></a>Отправка пакета событий (в схеме Event Grid)

**Запрос**:``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**Ответ**: HTTP 200, пустая полезная нагрузка


**Описание поля полезной нагрузки**
- ```Id```является обязательным. Это может быть любое значение строки, которое населены абонентом. Event Grid НЕ выполняет никаких дубликатов обнаружения или обеспечения соблюдения какой-либо семантики на этом поле.
- ```Topic```является необязательным, но если указанный должен соответствовать topic_name из URL запроса
- ```Subject```является обязательным, может быть любой строки значение
- ```EventType```является обязательным, может быть любой строки значение
- ```EventTime```является обязательным, это не проверено, но должно быть надлежащей DateTime.
- ```DataVersion``` является обязательным параметром.
- ```MetadataVersion```является необязательным, если указано это должно быть строка со значением```"1"```
- ```Data```является необязательным, и может быть любой токен JSON (число, строка, булейн, массив, объект)

### <a name="send-batch-of-events-in-custom-schema"></a>Отправка пакета событий (в пользовательской схеме)

**Запрос**:``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Ответ**: HTTP 200, пустая полезная нагрузка


**Ограничения полезной нагрузки**
- Должен быть массив событий.
- Каждая запись массива должна быть объектом JSON.
- Никаких других ограничений (кроме размера полезной нагрузки).

## <a name="examples"></a>Примеры

### <a name="set-up-topic-with-eventgrid-schema"></a>Настройка темы со схемой EventGrid
Настраивайте тему, требуя, чтобы события были опубликованы в **eventgridschema.**

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Настройка темы с пользовательской схемой
Настраивайте тему, требуя, чтобы `customschema`события были опубликованы в .

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Настройка темы с помощью схемы облачных событий
Настраивайте тему, требуя, чтобы `cloudeventschema`события были опубликованы в .

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Настройка WebHook как назначения, события, которые будут доставлены в eventgridschema
Используйте этот тип назначения для отправки событий в любой другой модуль (в котором размещается конечная точка HTTP) или в любую адресную конечную точку HTTP в сети/Интернете.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

Ограничения на `endpointUrl` атрибут:
- Он должен быть ненулевым.
- Он должен быть абсолютным URL.
- Если outbound__webhook__httpsOnly установлена в настройках EventGridModule, она должна быть только HTTPS.
- Если outbound__webhook__httpsOnly установлен на ложные, это может быть HTTP или HTTPS.

Ограничения на `eventDeliverySchema` имущество:
- Он должен соответствовать схеме ввода подписной темы.
- Может принимать значение NULL. Он по умолчанию влияет на схему ввода темы.

### <a name="set-up-iot-edge-as-destination"></a>Настройка IoT Edge в качестве пункта назначения

Используйте этот пункт назначения для отправки событий в Концентратор IoT Edge и подвергайтесь подсистеме концентратора края.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Настройка облака сетки событий в качестве пункта назначения

Используйте этот пункт назначения для отправки событий в сетку событий в облаке (Azure). Перед созданием подписки на краю необходимо сначала настроить тему пользователя в облаке, в который следует отправлять события.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl:
- Он должен быть ненулевым.
- Он должен быть абсолютным URL.
- Путь `/api/events` должен быть определен в пути URL-адреса запроса.
- Он должен `api-version=2018-01-01` быть в строке запроса.
- Если outbound__eventgrid__httpsOnly установлена в настройках EventGridModule (правда по умолчанию), она должна быть только HTTPS.
- Если outbound__eventgrid__httpsOnly установлен на ложные, это может быть HTTP или HTTPS.
- Если outbound__eventgrid__allowInvalidHostnames установлен на ложное (ложное по умолчанию), оно должно быть нацелено на одну из следующих конечных точек:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

Саски:
- Должно быть, ненулевый.

ТемаИмя:
- Если Subscription.EventDeliverySchema настроена на EventGridSchema, значение из этого поля ставится в поле темы каждого события перед переадресой в Сетку событий в облаке.
- Если Subscription.EventDeliverySchema настроена на CustomEventSchema, это свойство игнорируется, и пользовательская полезная нагрузка события перенаправляется точно так же, как она была получена.

## <a name="set-up-event-hubs-as-a-destination"></a>Настройка концентраторов событий в качестве пункта назначения

Чтобы опубликовать в концентратор `eventHub` событий, установите: `endpointType`

* строка подключения String: Строка подключения для конкретного концентратора событий, на который вы ориентируетесь, генерируемая с помощью политики общего доступа.

    >[!NOTE]
    > Строка соединения должна быть специфична сущностью. Использование строки подключения пространства имен не работает. Можно создать строку соединения, связанную с сущностью, перенаправившись в определенный концентратор событий, который вы хотели бы опубликовать в портале Azure, и нажав **на политики общего доступа** для создания новой строки connecection.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Настройка очередей служебных автобусов в качестве пункта назначения

Чтобы опубликовать в очередь автобуса `endpointType` службы, установите: `serviceBusQueue`

* строка подключения String: Строка подключения для конкретной очереди шин обслуживания, на которой вы ориентируетесь, генерируемая с помощью политики общего доступа.

    >[!NOTE]
    > Строка соединения должна быть специфична сущностью. Использование строки подключения пространства имен не работает. Создайте строку соединения, связанную с сущностью, перенаправившись в конкретную очередь в автобусслужбы, которую вы хотели бы опубликовать в портале Azure, и нажав **на политики общего доступа** для создания новой строки connecection.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>Настройка темы служебных автобусов в качестве пункта назначения

Чтобы опубликовать тему автобуса службы, установите `endpointType` и `serviceBusTopic` предоставьте:

* строка подключения String: Строка подключения для конкретной темы автобуса службы, на которую вы ориентируетесь, генерируемой с помощью политики общего доступа.

    >[!NOTE]
    > Строка соединения должна быть специфична сущностью. Использование строки подключения пространства имен не работает. Создайте строку соединения, связанную с сущностью, перенаправившись по конкретной теме шины службы, которую вы хотели бы опубликовать в портале Azure, и нажав **на политики общего доступа** для создания новой строки connecection.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>Настройка очередей хранения в качестве пункта назначения

Чтобы опубликовать очередь хранения, `endpointType` установите: `storageQueue`

* название очереди: Имя очереди хранения, в которое вы публикуете.
* connectionString: Строка подключения для учетной записи хранилища, в ней находится очередь хранения.

    >[!NOTE]
    > Unline Концентраторы событий, Очереди автобусов обслуживания, и Темы шины обслуживания, строка соединения, используемая для очередей хранения, не является специфическим объектом. Вместо этого он должен, но строка подключения для учетной записи хранения.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```