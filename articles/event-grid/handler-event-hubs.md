---
title: Использование концентратора событий как обработчика событий Сетки событий Azure
description: Узнайте, как можно использовать концентраторы событий в качестве обработчиков событий Сетки событий Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 4fb8027290831dbfed4b6d202d61d2d689274828
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629630"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Использование концентратора событий как обработчика событий Сетки событий Azure
Обработчик событий — это место, куда отправляются события. Обработчик выполняет действие для обработки события. Некоторые службы Azure автоматически настроены для обработки событий. Одна из них — это **Центры событий Azure**. 

**Центры событий** можно использовать, если решение получает больше событий от Сетки событий, чем может обработать. Когда события находятся в концентраторе событий, приложение может обрабатывать такие события по собственному расписанию. Вы можете расширить параметры обработки входящих событий.

## <a name="tutorials"></a>Учебники
См. следующие примеры. 

|Title  |Описание  |
|---------|---------|
| [Краткое руководство. Перенаправление пользовательских событий в Центры событий Azure с помощью Azure CLI](custom-event-to-eventhub.md) | Отправляет пользовательское событие в концентратор событий для обработки приложением. |
| [Шаблон Resource Manager. Создание пользовательского раздела Сетки событий и отправка событий в концентратор событий](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Шаблон Resource Manager, который создает подписку для пользовательского раздела. Он отправляет события в Центры событий Azure. |

## <a name="message-properties"></a>Свойства сообщений
Если вы используете **концентратор событий** в качестве обработчика событий для событий из сетки событий, это свойства, получаемые в заголовках сообщений: 

| Имя свойства | Описание |
| ------------- | ----------- | 
| aeg-subscription-name | Имя подписки на события. |
| aeg-delivery-count | <p>Число попыток, выполненных для события.</p> <p>Пример 1</p> |
| aeg-event-type | <p>Тип события.</p><p> Пример Microsoft.Storage.blobCreated</p> | 
| aeg-metadata-version | <p>Версия метаданных события.</p> <p>Пример 1.</p><p> Для **схемы событий Сетки событий** это свойство представляет версию метаданных, а для **схемы событий облака **—** версию спецификации**. </p>|
| aeg-data-version | <p>Версия данных события.</p><p>Пример 1.</p><p>Для **схемы событий Сетки событий** это свойство представляет версию данных, а для **схемы событий облака** оно не используется.</p> |
| aeg-output-event-id | Идентификатор события Сетки событий. |

## <a name="rest-examples-for-put"></a>Примеры REST (для PUT)


### <a name="event-hub"></a>Концентратор событий

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "EventHub",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>Концентратор событий — доставка с использованием управляемого удостоверения

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "EventHub",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
См. список поддерживаемых обработчиков событий в статье [Обработчики событий](event-handlers.md). 
