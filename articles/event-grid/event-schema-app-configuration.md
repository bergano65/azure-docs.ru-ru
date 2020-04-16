---
title: Конфигурация приложения Azure как источник сетки событий
description: В этой статье описывается, как использовать конфигурацию приложения Azure в качестве источника событийeventной сети событий. Он предоставляет схему и ссылки на учебник и как к статьям.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: adb548ef8531698a2cb075fbc742bb20a02a434b
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393429"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Конфигурация приложения Azure как источник сетки событий
В этой статье приведены свойства и схема для событий конфигурации приложений Azure. Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md). Он также дает вам список быстрых стартов и учебников для использования конфигурации приложений Azure в качестве источника событий.

## <a name="event-grid-event-schema"></a>Схема событий службы "Сетка событий Azure"

### <a name="available-event-types"></a>Доступные типы событий

Конфигурация приложения Azure излучает следующие типы событий:

| Тип события | Описание |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueМодифицировано | Поднят оцениваемый при создании или замене ключевой стоимости. |
| Microsoft.AppConfiguration.KeyValueDeleted | Поднято при удалении значения ключа. |

### <a name="example-event"></a>Пример события

Следующий пример показывает схему измененного события с ключевой стоимостью: 

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

Схема для удаленного события с ключевым значением аналогична: 

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
 
### <a name="event-properties"></a>Свойства события

Событие содержит следующие высокоуровневые данные:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| Раздел | строка | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | строка | Определенный издателем путь к субъекту событий. |
| eventType | строка | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | строка | Время создания события с учетом времени поставщика в формате UTC. |
| ID | строка | Уникальный идентификатор события. |
| . | объект | Данные о событиях конфигурации приложения. |
| dataVersion | строка | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | строка | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| ключ | строка | Ключ ключевой значения, который был изменен или удален. |
| label | строка | Метка, если таковая имеется, из ключевой значения, которая была изменена или удалена. |
| etag | строка | Для `KeyValueModified` etag нового ключевого значения. Для `KeyValueDeleted` etag ключевой значения, которая была удалена. |

## <a name="tutorials-and-how-tos"></a>Учебники и как-tos

|Title | Описание |
|---------|---------|
| [Реагируйте на события конфигурации приложений Azure с помощью Event Grid](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Обзор интеграции конфигурации приложений Azure с Event Grid. |
| [Быстрый запуск: маршрут событий конфигурации приложения Azure к пользовательской веб-конечной точке с Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Показывает, как использовать Azure CLI для отправки событий конфигурации приложений Azure в WebHook. |

## <a name="next-steps"></a>Дальнейшие действия

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
* Для ознакомительсь с событиями [Route Azure App Configuration events - Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json)конфигурации приложений Azure, см. 