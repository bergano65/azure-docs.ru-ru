---
title: Схемы событий - Azure Event Grid IoT Edge Документы Майкрософт
description: Схемы событий в Event Grid на IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242470"
---
# <a name="event-schemas"></a>Схемы событий

Модуль Event Grid принимает и доставляет события в формате JSON. В настоящее время существуют три схемы, которые поддерживаются Event Grid: -

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

Можно настроить схему, которую должен соответствовать издатель при создании темы. Если не указано, он по умолчанию **EventGridSchema**. События, не соответствующие ожидаемой схеме, будут отклонены.

Абоненты также могут настроить схему, в которой они хотят, чтобы события доставлены. Если не указано, по умолчанию является схема темы.
В настоящее время схема доставки абонента должна соответствовать схеме ввода своей темы. 

## <a name="eventgrid-schema"></a>Схема EventGrid

Схема EventGrid состоит из набора требуемых свойств, которым должна соответствовать издательская организация. Каждый издатель должен заполнить поля верхнего уровня.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>Свойства схемы EventGrid

Все события имеют следующие данные верхнего уровня:

| Свойство | Тип | Обязательно | Описание |
| -------- | ---- | ----------- |-----------
| Раздел | строка | нет | Должен совпадать с темой, по которой он опубликован. Event Grid заполняет его названием темы, по которой она опубликована, если не уточняется. |
| subject | строка | Да | Определенный издателем путь к субъекту событий. |
| eventType | строка | Да | Тип события для этого источника событий, например, BlobCreated. |
| eventTime | строка | Да | Время создания события с учетом времени поставщика в формате UTC. |
| ID | строка | нет | Уникальный идентификатор события. |
| . | объект | нет | Используется для сбора данных событий, характерных для сущности публикации. |
| dataVersion | строка | Да | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | строка | нет | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

### <a name="example--eventgrid-schema-event"></a>Пример - Событие схемы Event Event

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>Схема CustomEvent

В пользовательской схеме нет обязательных свойств, которые применяются, как схема EventGrid. Элементы публикации могут полностью контролировать схему события. Он обеспечивает максимальную гибкость и позволяет создавать сценарии, в которых уже есть система, основанная на событиях, и она хотела бы повторно использовать существующие события и/или не хотите быть привязанными к конкретной схеме.

### <a name="custom-schema-properties"></a>Пользовательские свойства схемы

Нет обязательных свойств. Определить полезную нагрузку должна организация-издатель.

### <a name="example--custom-schema-event"></a>Пример - Событие пользовательской схемы

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>Схема CloudEvent

В дополнение к вышеуказанным схемам, Event Grid намеренно поддерживает события в [схеме CloudEvents JSON.](https://github.com/cloudevents/spec/blob/master/json-format.md) CloudEvents — открытая спецификация для описания данных о событиях. Это упрощает совместимость, предоставляя общую схему событий для публикации и потребления событий. Он является частью [CNCF](https://www.cncf.io/) и в настоящее время доступна версия 1.0-rc1.

### <a name="cloudevent-schema-properties"></a>Свойства схемы CloudEvent

Обратитесь к [спецификации CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) об обязательных свойствах конверта.

### <a name="example--cloud-event"></a>Пример — облачное событие
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
