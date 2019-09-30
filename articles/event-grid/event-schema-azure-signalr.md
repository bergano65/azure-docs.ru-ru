---
title: Схема событий SignalR Azure сетки событий Azure
description: Описывает свойства, предоставленные для события Azure SignalR с помощью сетки событий Azure
services: event-grid
author: chenyl
ms.service: event-grid
ms.topic: reference
ms.date: 06/11/2019
ms.author: chenyl
ms.openlocfilehash: 3b072ff2b680ad6d144c7441190ab2df9870f5d0
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789076"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Схема событий Azure "Сетка событий" для службы SignalR

В этой статье описаны свойства и схема событий службы SignalR. Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md).


## <a name="available-event-types"></a>Доступные типы событий

Служба SignalR выдает следующие типы событий:

| Тип события | Описание |
| ---------- | ----------- |
| Microsoft.SignalRService.ClientConnectionConnected | Возникает при подключении клиентского соединения. |
| Microsoft.SignalRService.ClientConnectionDisconnected | Вызывается при отключении подключения клиента. |

## <a name="example-event"></a>Пример события

Пример схемы, клиента подключенного событие соединения: 

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

Схема для отключенных события подключения клиента аналогична: 

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

## <a name="event-properties"></a>Свойства события

Событие содержит следующие высокоуровневые данные:

| Свойство | type | Описание |
| -------- | ---- | ----------- |
| topic | string | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | string | Определенный издателем путь к субъекту событий. |
| eventType | string | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | string | Время создания события с учетом времени поставщика в формате UTC. |
| id | string | Уникальный идентификатор события. |
| data | object | Данные события службы SignalR. |
| dataVersion | string | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | string | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | type | Описание |
| -------- | ---- | ----------- |
| timestamp | string | Время создания события с учетом времени поставщика в формате UTC. |
| hubName | string | Концентратор, который принадлежит клиентского соединения. |
| connectionId | string | Уникальный идентификатор для клиентского соединения. |
| userId | string | Идентификатор пользователя, определенные в утверждении. |
| errorMessage | string | Ошибка, которая приводит к отключении подключения. |

## <a name="next-steps"></a>Следующие шаги

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
