---
title: Реагирование на события службы Azure SignalR
description: Используйте службу "Сетка событий Azure" для подписки на события службы Azure SignalR.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 06/12/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a3d0669a1a89f2fc5aaca0a96e00b731d2d40830
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296836"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Реагирование на события службы Azure SignalR

События службы Azure SignalR позволяют приложениям реагировать на подключения клиентов, подключенные или отключенные с помощью современных бессерверных архитектур. При этом не требуется сложный код или дорогостоящие и неэффективные службы опроса.  Вместо этого события отправляются через службу [Сетка событий Azure](https://azure.microsoft.com/services/event-grid/) подписчикам, таким как [Функции Azure](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), или даже в настраиваемый вами прослушиватель HTTP. При этом вы оплачиваете только то, что используете.

События службы Azure SignalR надежно отправляются в службу "Сетка событий", которая обеспечивает надежную работу служб доставки для приложений с помощью многофункциональных политик повтора и доставки недоставленных сообщений. Дополнительные сведения см. в разделе [Доставка сообщений в сетке событий и повторная попытка](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Модель Сетки событий](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Бессерверное состояние
События службы Azure SignalR активны только в том случае, если клиентские соединения находятся в состоянии без сервера. В общем случае, если клиент не направляется на основной сервер, он переходит в бессерверное состояние. Классический режим работает только в том случае, если концентратор, к которому подключаются подключения клиентов, не имеет сервера-концентратора. Однако для предотвращения некоторой проблемы рекомендуется использовать бессерверный режим. Дополнительные сведения о режиме обслуживания см. в разделе [как выбрать режим обслуживания](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Доступные события службы SignalR Azure
Сетка событий использует [подписки на события](../event-grid/concepts.md#event-subscriptions) для маршрутизации сообщений о событиях подписчикам. Подписки на события службы Azure SignalR поддерживают два типа событий:  

|Название мероприятия|Описание|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Возникает при подключении клиентского подключения.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Возникает при отключении клиентского соединения.|

## <a name="event-schema"></a>Схема событий
События службы Azure SignalR содержат всю информацию, необходимую для реагирования на изменения в данных. Вы можете указать событие службы Azure SignalR со свойством eventType, начинающимся с "Microsoft. Сигналрсервице". Дополнительные сведения об использовании свойств событий сетки событий описаны в статье [схема событий сетки](../event-grid/event-schema.md)событий.  

Ниже приведен пример события подключения клиента:
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

Дополнительные сведения см. в статье [схема событий службы SignalR](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о службе "Сетка событий Azure" и предоставление им событий службы SignalR см. в этой статье.

> [!div class="nextstepaction"]
> [Попробуйте использовать пример интеграции сетки событий со службой "служба Azure SignalR](./signalr-howto-event-grid-integration.md)
> " о службе "[Сетка событий](../event-grid/overview.md) "
