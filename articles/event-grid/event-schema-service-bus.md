---
title: Служба "служебная шина Azure" как источник "Сетка событий"
description: Описание свойств для событий служебной шины, используемых со службой "Сетка событий Azure"
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: 141a0e96071014dc3705d30f72b1a9257737298a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393240"
---
# <a name="azure-service-bus-as-an-event-grid-source"></a>Служебная шина Azure в качестве источника службы "Сетка событий"

В этой статье описаны свойства и схема для событий служебной шины.Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md).

## <a name="event-grid-event-schema"></a>Схема событий службы "Сетка событий Azure"

### <a name="available-event-types"></a>Доступные типы событий

Служебная шина выдает следующие типы событий:

| Тип события. | Описание |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Возникает, если в очереди или подписке присутствуют активные сообщения, а получатели отсутствуют. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Возникает, если в очереди недоставленных сообщений присутствуют активные сообщения, а активные прослушиватели отсутствуют. |

### <a name="example-event"></a>Пример события

В следующем примере показана схема события активных сообщений без прослушивателей:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Схема события очереди недоставленных сообщений выглядит похоже:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Свойства события

Событие содержит следующие высокоуровневые данные:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| Раздел | строка | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | строка | Определенный издателем путь к субъекту событий. |
| eventType | строка | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | строка | Время создания события с учетом времени поставщика в формате UTC. |
| идентификатор | строка | Уникальный идентификатор события. |
| . | object | Данные события хранилища BLOB-объектов. |
| dataVersion | строка | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | строка | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| namespaceName | строка | Пространство имен служебной шины, в котором существует ресурс. |
| requestUri | строка | URI определенной очереди или подписки, создающей события. |
| entityType | строка | Тип сущности служебной шины, создающей события (очередь или подписка). |
| queueName | строка | Очередь с активными сообщениями при подписке на очередь. Значение null, если используются разделы и подписки. |
| topicName | строка | Раздел, к которому принадлежит подписка служебной шины с активными сообщениями. Значение null, если используется очередь. |
| subscriptionName | строка | Подписка служебной шины с активными сообщениями. Значение null, если используется очередь. |

## <a name="tutorials-and-how-tos"></a>Руководства и инструкции
|Заголовок  |Описание  |
|---------|---------|
| [Примеры интеграции служебной шины Azure со службой "Сетка событий Azure"](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Служба "Сетка событий" отправляет сообщения из раздела служебной шины в приложение-функцию и приложение логики. |
| [Интеграция служебной шины Azure в службу "Сетка событий"](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Общие сведения об интеграции служебной шины со службой "Сетка событий". |

## <a name="next-steps"></a>Дальнейшие действия

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
* Дополнительные сведения об использовании сетки событий Azure со служебной шиной см. в статье [Общие сведения об интеграции служебной шины Azure со службой "Сетка событий Azure"](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Попробуйте [получать события служебной шины с помощью функций или приложений Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
