---
title: Кэш Azure для Redis в качестве источника службы "Сетка событий"
description: Описание свойств кэша Azure для событий Redis с помощью службы "Сетка событий Azure"
ms.topic: conceptual
ms.date: 12/21/2020
author: curib
ms.author: cauribeg
ms.openlocfilehash: f446f3f469a7404e6e74ba67ee24bf32578fe9d8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056222"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Кэш Azure для Redis в качестве источника службы "Сетка событий"

В этой статье приводятся свойства и схема для событий Redis в кэше Azure. Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md). 

## <a name="event-grid-event-schema"></a>Схема событий службы "Сетка событий Azure"

### <a name="list-of-events-for-azure-cache-for-redis-rest-apis"></a>Список событий кэша Azure для API-интерфейсов Redis

Эти события активируются при экспорте, импорте или масштабировании клиента путем вызова Azure Cache для интерфейсов API Redis. Событие исправления инициируется обновлением Redis.

 |Имя события |Описание|
 |----------|-----------|
 |**Microsoft. Cache. Експортрдбкомплетед** |Активируется при экспорте данных кэша. |
 |**Microsoft. Cache. Импортрдбкомплетед** |Активируется при импорте данных кэша. |
 |**Microsoft. Cache. Патчингкомплетед** |Активируется после завершения установки исправлений. |
 |**Microsoft. Cache. Скалингкомплетед** |Активируется при завершении масштабирования. |

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>Содержимое ответа на событие

При активации события служба Сетки событий отправляет данные о нем на подписанную конечную точку.

В этом разделе содержится пример того, как будут выглядеть данные для каждого события кэша Azure для Redis.

### <a name="microsoftcachepatchingcompleted-event"></a>Событие Microsoft. Cache. Патчингкомплетед

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.PatchingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"PatchingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"PatchingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Событие Microsoft. Cache. Импортрдбкомплетед

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ImportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ImportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ImportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Событие Microsoft. Cache. Експортрдбкомплетед

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ExportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ExportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ExportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcachescalingcompleted"></a>Microsoft. Cache. Скалингкомплетед

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ScalingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ScalingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ScalingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
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
| . | объект | Кэш Azure для данных событий Redis. |
| dataVersion | строка | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | строка | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| TIMESTAMP | строка | Время возникновения события. |
| name | строка | Имя события. |
| status | строка | Состояние события. Сбой или успешно выполнен. |


## <a name="quickstarts"></a>Краткие руководства

Если вы хотите попробовать использовать кэш Azure для событий Redis, см. любую из следующих кратких руководств:

|Если вы хотите использовать это средство:    |См. следующую статью: |
|--|-|
|Портал Azure    |[Краткое руководство. Маршрутизация кэша Azure для событий Redis в веб-конечную точку с помощью портал Azure](../azure-cache-for-redis/cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Краткое руководство. Маршрутизация кэша Azure для событий Redis в веб-конечную точку с помощью PowerShell](../azure-cache-for-redis/cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[Краткое руководство. Маршрутизация кэша Azure для событий Redis в веб-конечную точку с Azure CLI](../azure-cache-for-redis/cache-event-grid-quickstart-cli.md)|

## <a name="next-steps"></a>Дальнейшие действия

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).

