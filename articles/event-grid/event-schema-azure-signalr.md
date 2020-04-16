---
title: Azure SingnalR — источник event Grid
description: Описывает свойства, которые предусмотрены для событий Azure SignalR с помощью Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: 730d1a7a053ab636c45313dd0c35a537434eb782
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393390"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Схема событий Azure Event Grid для службы SignalR

В этой статье приведены свойства и схема для событий службы SignalR.Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md). Он также дает вам список быстрых стартов и учебников для использования Azure SignalR в качестве источника событий.

## <a name="event-grid-event-schema"></a>Схема событий службы "Сетка событий Azure"

### <a name="available-event-types"></a>Доступные типы событий

Служба SignalR излучает следующие типы событий:

| Тип события | Описание |
| ---------- | ----------- |
| Microsoft.SignalRService.КлиентПодключено | Поднят оподключено при подключении клиентского соединения. |
| Microsoft.SignalRService.ClientConnectionDisconnected | Поднят при отключении клиентского соединения. |

### <a name="example-event"></a>Пример события

Следующий пример показывает схему события, связанного с подключением к клиенту: 

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

Схема для отключенного события клиентского соединения аналогична: 

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

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| Раздел | строка | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | строка | Определенный издателем путь к субъекту событий. |
| eventType | строка | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | строка | Время создания события с учетом времени поставщика в формате UTC. |
| идентификатор | строка | Уникальный идентификатор события. |
| . | объект | Данные событий службы SignalR. |
| dataVersion | строка | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | строка | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| TIMESTAMP | строка | Время создания события с учетом времени поставщика в формате UTC. |
| hubName | строка | Концентратор, к которому принадлежит клиентское соединение. |
| connectionId | строка | Уникальный идентификатор для клиентского соединения. |
| userId | строка | Идентификатор пользователя, определенный в претензии. |
| Errormessage | строка | Ошибка, которая приводит к отключению соединения. |

## <a name="tutorials-and-how-tos"></a>Учебники и как-tos
|Title | Описание |
|---------|---------|
| [Реагируйте на события службы Azure SignalR с помощью Event Grid](../azure-signalr/signalr-concept-event-grid-integration.md) | Обзор интеграции службы Azure SignalR с Event Grid. |
| [Как отправить события службы Azure SignalR в сетку событий](../azure-signalr/signalr-howto-event-grid-integration.md) | Показывает, как отправлять события службы Azure SignalR в приложение через Event Grid. |

## <a name="next-steps"></a>Дальнейшие действия

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
