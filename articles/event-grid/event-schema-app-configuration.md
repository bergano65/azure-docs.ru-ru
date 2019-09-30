---
title: Схема событий Конфигурация приложения Azure сетки событий Azure
description: Описывает свойства, предоставленные для события настройки приложения Azure с помощью сетки событий Azure
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735786"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Схема событий Azure "Сетка событий" для конфигурации приложений Azure

В этой статье предоставляет свойства и схема для событий конфигурации приложения Azure. Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md).

Список примеров сценариев и учебники, см. в разделе [источник события конфигурации приложения Azure](event-sources.md#app-configuration).

## <a name="available-event-types"></a>Доступные типы событий

Настройка приложения Azure выдает следующие типы событий:

| Тип события | Описание |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Возникает при создании или замене значения ключа. |
| Microsoft.AppConfiguration.KeyValueDeleted | Возникает при удалении значения ключа. |

## <a name="example-event"></a>Пример события

В следующем примере показана схема события измененного ключ значение: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Схема для событие deleted ключ значение аналогична: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
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
| data | object | Данные события конфигурации приложения. |
| dataVersion | string | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | string | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | type | Описание |
| -------- | ---- | ----------- |
| key | string | Ключ, ключ-значение, которая была изменена или удалена. |
| label | string | Метка, если таковое имеется, ключ-значение, которая была изменена или удалена. |
| etag | string | Для `KeyValueModified` etag новый ключ значение. Для `KeyValueDeleted` etag ключ значение, который был удален. |
 
## <a name="next-steps"></a>Дальнейшие действия

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
* Общие сведения о работе с событиями Конфигурация приложения Azure, см. в разделе [Конфигурация приложения Azure маршрут события — Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 