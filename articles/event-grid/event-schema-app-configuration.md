---
title: Настройка приложения Azure в качестве источника службы "Сетка событий"
description: В этой статье описывается, как использовать конфигурацию приложения Azure в качестве источника событий службы "Сетка событий". Она содержит схему и ссылки на учебники и практические руководства.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d305236e8408052be4be28ec003f4e545119fc59
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550680"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Настройка приложения Azure в качестве источника службы "Сетка событий"
В этой статье содержатся свойства и схема событий конфигурации приложений Azure. Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md). Здесь также приводится список быстрых запусков и учебников по использованию конфигурации приложений Azure в качестве источника событий.

## <a name="event-grid-event-schema"></a>Схема событий службы "Сетка событий Azure"

### <a name="available-event-types"></a>Доступные типы событий

В конфигурации приложений Azure выдаются следующие типы событий:

| Тип события | Описание |
| ---------- | ----------- |
| Microsoft. Аппконфигуратион. Кэйвалуемодифиед | Возникает при создании или замене ключевого значения. |
| Microsoft. Аппконфигуратион. Кэйвалуеделетед | Вызывается при удалении значения ключа. |

### <a name="example-event"></a>Пример события

В следующем примере показана схема события изменения ключа "ключ — значение": 

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

Схема для события Deleted-valued имеет следующий вид: 

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
| . | объект | Данные события конфигурации приложения. |
| dataVersion | строка | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | строка | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| ключ | строка | Ключ измененного или удаленного ключевого значения. |
| метка | строка | Метка, если таковая имеется, для измененного или удаленного ключевого значения. |
| etag | строка | Для `KeyValueModified` ETag нового ключевого значения. Для `KeyValueDeleted` ETag удаленного ключевого значения. |

## <a name="tutorials-and-how-tos"></a>Руководства и инструкции

|Заголовок | Описание |
|---------|---------|
| [Реагирование на события настройки приложения Azure с помощью службы "Сетка событий"](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Общие сведения об интеграции конфигурации приложений Azure с сеткой событий. |
| [Использовать сетку событий для уведомлений об изменении данных](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Узнайте, как использовать подписки на события конфигурации приложений Azure для отправки событий изменения значения ключа в веб-конечную точку. |

## <a name="next-steps"></a>Дальнейшие действия

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
* Общие сведения о работе с событиями конфигурации приложений Azure см. в статье Использование службы " [Сетка событий" для уведомлений об изменении данных](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 