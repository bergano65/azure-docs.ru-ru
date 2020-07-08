---
title: Azure SignalR в качестве источника службы "Сетка событий"
description: Описание свойств, предоставленных для событий Azure SignalR с помощью службы "Сетка событий Azure"
services: event-grid
author: femila
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: femila
ms.openlocfilehash: 8fbae3fad4aeb85022c804e1ac648060360c6531
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84560634"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Схема событий сетки событий Azure для службы SignalR

В этой статье приводятся свойства и схема событий службы SignalR.Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md). Здесь также приводится список быстрых запусков и руководств по использованию Azure SignalR в качестве источника событий.

## <a name="event-grid-event-schema"></a>Схема событий службы "Сетка событий Azure"

### <a name="available-event-types"></a>Доступные типы событий

Служба SignalR выдает следующие типы событий:

| Тип события | Описание |
| ---------- | ----------- |
| Microsoft. Сигналрсервице. Клиентконнектионконнектед | Возникает, когда соединение с клиентом установлено. |
| Microsoft. Сигналрсервице. Клиентконнектиондисконнектед | Возникает, когда клиентское соединение разорвано. |

### <a name="example-event"></a>Пример события

В следующем примере показана схема события подключения клиента: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Схема для события отключенного подключения клиента аналогична: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Свойства события

Событие содержит следующие высокоуровневые данные:

| Свойство. | Type | Описание: |
| -------- | ---- | ----------- |
| Раздел | string | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | string | Определенный издателем путь к субъекту событий. |
| eventType | string | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | string | Время создания события с учетом времени поставщика в формате UTC. |
| идентификатор | строка | Уникальный идентификатор события. |
| . | объект | Данные события службы SignalR. |
| dataVersion | string | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | string | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | Type | Описание: |
| -------- | ---- | ----------- |
| TIMESTAMP | string | Время создания события с учетом времени поставщика в формате UTC. |
| hubName | string | Концентратор, к которому принадлежит клиентское соединение. |
| connectionId | string | Уникальный идентификатор клиентского соединения. |
| userId | строка | Идентификатор пользователя, определенный в утверждении. |
| errorMessage | string | Ошибка, вызывающая отключение подключения. |

## <a name="tutorials-and-how-tos"></a>Руководства и инструкции
|Заголовок | Описание |
|---------|---------|
| [Реагирование на события службы SignalR Azure с помощью сетки событий](../azure-signalr/signalr-concept-event-grid-integration.md) | Общие сведения об интеграции службы Azure SignalR со службой "Сетка событий". |
| [Отправка событий службы Azure SignalR в службу "Сетка событий"](../azure-signalr/signalr-howto-event-grid-integration.md) | Показано, как отправить события службы Azure SignalR в приложение через службу "Сетка событий". |

## <a name="next-steps"></a>Дальнейшие шаги

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
