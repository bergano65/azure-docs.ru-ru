---
title: Схема событий концентраторов в службе "Сетка событий Azure"
description: Описание свойств для событий концентраторов, используемых со службой "Сетка событий Azure"
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 9c0113687d27bf43375f298057129a5594ec0a06
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561834"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Схема событий службы "Сетка событий Azure" для концентраторов событий

В этой статье описаны свойства и схема для событий концентраторов. Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md).

Список примеров сценариев и руководства см. в статье [Источники событий в службе "Сетка событий Azure"](event-sources.md#event-hubs).

### <a name="available-event-types"></a>Доступные типы событий

Тип события **Microsoft.EventHub.CaptureFileCreated** возникает в службе "Центры событий" при создании файла записи.

## <a name="example-event"></a>Пример события

Этот пример события демонстрирует схему события в Центрах событий, возникающего, когда файл сохраняется при помощи функции Сбора: 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

## <a name="event-properties"></a>Свойства события

Событие содержит следующие высокоуровневые данные:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| topic | string | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | string | Определенный издателем путь к субъекту событий. |
| eventType | string | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | string | Время создания события с учетом времени поставщика в формате UTC. |
| id | string | Уникальный идентификатор события. |
| data | object | Данные события концентратора. |
| dataVersion | string | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | string | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| fileUrl | string | Путь к файлу записи. |
| fileType | string | Тип файла записи. |
| partitionId | string | Идентификатор сегмента. |
| sizeInBytes | целое число | Размер файла. |
| eventCount | целое число | Число событий в файле. |
| firstSequenceNumber | целое число | Наименьший порядковый номер в очереди. |
| lastSequenceNumber | целое число | Последний порядковый номер в очереди. |
| firstEnqueueTime | string | Первые данные времени в очереди. |
| lastEnqueueTime | string | Последние данные времени в очереди. |

## <a name="next-steps"></a>Дальнейшие действия

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
* Сведения об обработке событий в Центрах событий см. в статье [Потоковая передача больших данных в хранилище данных](event-grid-event-hubs-integration.md).
