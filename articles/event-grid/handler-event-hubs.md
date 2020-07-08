---
title: Использование концентратора событий как обработчика событий Сетки событий Azure
description: Узнайте, как можно использовать концентраторы событий в качестве обработчиков событий Сетки событий Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: f46b17fdffc870e6afc5f3b0711169db8270a540
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800440"
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
Если вы используете **концентратор событий** в качестве обработчика событий Сетки событий, определите следующие заголовки сообщений: 

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
