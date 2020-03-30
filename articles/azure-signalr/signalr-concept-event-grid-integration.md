---
title: Реагировать на события службы Azure SignalR
description: Используйте Azure Event Grid для подписки на события службы Azure SignalR. Другие службы ниже по течению могут быть вызваны этими событиями.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158190"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reacting to Azure SignalR Service events (Реагирование на события службы Azure SignalR)

События службы Azure SignalR позволяют приложениям реагировать на подключенные к клиентским соединениям или отключенные с помощью современных архитектур без серверов. При этом не требуется сложный код или дорогостоящие и неэффективные службы опроса.  Вместо этого события отправляются через службу [Сетка событий Azure](https://azure.microsoft.com/services/event-grid/) подписчикам, таким как [Функции Azure](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), или даже в настраиваемый вами прослушиватель HTTP. При этом вы оплачиваете только то, что используете.

События службы Azure SignalR надежно отправляются в службу Event Grid, которая предоставляет надежные услуги доставки вашим приложениям посредством богатых политик повтора и доставки букв. Чтобы узнать больше, смотрите [доставку сообщений Event Grid и повторную попытку.](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)

![Модель Сетки событий](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Состояние без серверов
События службы Azure SignalR активны только тогда, когда клиентские соединения находятся в безсерверном состоянии. Вообще говоря, если клиент не направляется на сервер концентратора, он переходит в состояние без сервера. Классический режим работает только тогда, когда концентратор, к которым подключается клиентские соединения, не имеет сервера концентратора. Тем не менее, режим без сервера рекомендуется, чтобы избежать некоторых проблем. Чтобы узнать больше о режиме обслуживания, [см.](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)

## <a name="available-azure-signalr-service-events"></a>Доступные события службы Azure SignalR
Сетка событий использует [подписки на события](../event-grid/concepts.md#event-subscriptions) для маршрутизации сообщений о событиях подписчикам. Подписи к событиям Azure SignalR Service поддерживают два типа событий:  

|Название мероприятия|Описание|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Поднят при подключении клиентского соединения.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Поднят при отключении клиентского соединения.|

## <a name="event-schema"></a>Схема событий
События службы Azure SignalR содержат всю информацию, необходимую для ответа на изменения в данных. Вы можете определить событие службы Azure SignalR с свойством eventType, которое начинается с "Microsoft.SignalRService". Дополнительная информация об использовании свойств событий Event Grid задокументирована в [схеме событий Event Grid.](../event-grid/event-schema.md)  

Вот пример события, связанного с подключением к клиенту:
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

Для получения дополнительной [SignalR Service events schema](../event-grid/event-schema-azure-signalr.md)информации см.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о Event Grid и предоставь azure SignalR Service:

> [!div class="nextstepaction"]
> [Попробуйте пример интеграции Event Grid с сервисом](./signalr-howto-event-grid-integration.md)
> Azure SignalR[о сетке событий](../event-grid/overview.md)
