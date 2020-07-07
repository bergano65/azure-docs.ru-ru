---
title: Концентраторы событий Azure в качестве источника службы "Сетка событий"
description: Описание свойств для событий концентраторов, используемых со службой &quot;Сетка событий Azure&quot;
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: fd65c20f07a091fa1fc8a6cbf003986e1096ebe3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81393337"
---
# <a name="azure-event-hubs-as-an-event-grid-source"></a>Концентратор событий Azure в качестве источника службы "Сетка событий"

В этой статье описаны свойства и схема для событий концентраторов.Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md).

## <a name="event-grid-event-schema"></a>Схема событий службы "Сетка событий Azure"

### <a name="available-event-types"></a>Доступные типы событий

Тип события **Microsoft.EventHub.CaptureFileCreated** возникает в службе "Центры событий" при создании файла записи.

### <a name="example-event"></a>Пример события

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

### <a name="event-properties"></a>Свойства события

Событие содержит следующие высокоуровневые данные:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| Раздел | строка | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | строка | Определенный издателем путь к субъекту событий. |
| eventType | строка | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | строка | Время создания события с учетом времени поставщика в формате UTC. |
| идентификатор | строка | Уникальный идентификатор события. |
| . | объект | Данные события концентратора. |
| dataVersion | строка | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | строка | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | Тип | Описание: |
| -------- | ---- | ----------- |
| fileUrl | строка | Путь к файлу записи. |
| fileType | строка | Тип файла записи. |
| partitionId | строка | Идентификатор сегмента. |
| sizeInBytes | Целое число | Размер файла. |
| eventCount | Целое число | Число событий в файле. |
| firstSequenceNumber | Целое число | Наименьший порядковый номер в очереди. |
| lastSequenceNumber | Целое число | Последний порядковый номер в очереди. |
| firstEnqueueTime | строка | Первые данные времени в очереди. |
| lastEnqueueTime | строка | Последние данные времени в очереди. |

## <a name="tutorials-and-how-tos"></a>Руководства и инструкции

|Заголовок  |Описание  |
|---------|---------|
| [Потоковая передача больших данных в хранилище данных](event-grid-event-hubs-integration.md) | Когда Центры событий создают файл сбора, служба "Сетка событий" отправляет событие приложению-функции. Это приложение извлекает файл сбора и переносит данные в хранилище данных. |

## <a name="next-steps"></a>Дальнейшие действия

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
* Сведения об обработке событий в Центрах событий см. в статье [Потоковая передача больших данных в хранилище данных](event-grid-event-hubs-integration.md).