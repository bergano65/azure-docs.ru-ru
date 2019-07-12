---
title: Реагирование на события служба Azure SignalR
description: Используйте "Сетка событий Azure" для подписки на события служба Azure SignalR.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 06/12/2019
ms.topic: conceptual
ms.service: azure-signalr
ms.openlocfilehash: 02f88c5953d499b30f2ea3408318f70a72f42b0f
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789193"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Реагирование на события служба Azure SignalR

События SignalR службы Azure позволяют приложениям реагировать на клиентские соединения подключен или отключен с помощью современных бессерверных архитектур. При этом не требуется сложный код или дорогостоящие и неэффективные службы опроса.  Вместо этого события отправляются через службу [Сетка событий Azure](https://azure.microsoft.com/services/event-grid/) подписчикам, таким как [Функции Azure](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), или даже в настраиваемый вами прослушиватель HTTP. При этом вы оплачиваете только то, что используете.

События Azure служба SignalR надежно отправляются в службу "Сетка событий", которая предоставляет надежную доставку служб приложений за счет мощных функций повторов, политики и доставки недоставленных. Дополнительные сведения см. в разделе [доставки сообщений сетки событий и повторите попытку](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Модель Сетки событий](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Без сервера состояния
События Azure служба SignalR активны только клиентские соединения осуществляются в состоянии без сервера. Вообще говоря Если клиент не отправляется на сервер-концентратор, он переходит в состояние без сервера. Классический режим работы, только в том случае, если концентратор, который подключения клиентских подключений, не имеет сервер-концентратор. Тем не менее во избежание проблем рекомендуется бессерверной режим. Чтобы получить дополнительные сведения о режиме службы, см. в разделе [как выбрать режим службы](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Доступные события служба Azure SignalR
Сетка событий использует [подписки на события](../event-grid/concepts.md#event-subscriptions) для маршрутизации сообщений о событиях подписчикам. Подписки на события Azure служба SignalR поддерживает два типа событий:  

|Название мероприятия|Описание|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Возникает при подключении клиентского соединения.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Вызывается, когда произойдет отключение соединения клиента.|

## <a name="event-schema"></a>Схема событий
Azure служба SignalR события содержат все сведения, необходимые для реагирования на изменения в данных. Вы можете определить SignalR службе Azure событие с свойство eventType начинается с «Microsoft.SignalRService». Дополнительные сведения об использовании свойств событий сетки событий описан в [схема событий службы "Сетка событий"](../event-grid/event-schema.md).  

Ниже приведен пример события подключенных подключения клиента:
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

Дополнительные сведения см. в разделе [схема событий службы SignalR](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о сетке событий и событий служба Azure SignalR, попробуйте:

> [!div class="nextstepaction"]
> [Попробуйте к интеграции "Сетка событий" образец с помощью службы Azure SignalR](./signalr-howto-event-grid-integration.md)
> [сведения о сетке событий](../event-grid/overview.md)
