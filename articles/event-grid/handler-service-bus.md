---
title: Очереди и разделы служебной шины в качестве обработчиков событий для событий службы "Сетка событий Azure"
description: В статье показано, как использовать очереди и разделы служебной шины в качестве обработчиков событий для событий службы "Сетка событий Azure".
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c573f7ee088fe1d88f832623891377d4fd50bd4b
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105699"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Очереди и разделы служебной шины в качестве обработчиков событий для событий службы "Сетка событий Azure"
Обработчик событий — это место, куда отправляются события. Обработчик выполняет последующую обработку полученного события. Некоторые службы Azure автоматически настроены для обработки событий. Одна из них — это **Служебная шина Azure**. 

Очередь или раздел служебной шины можно использовать в качестве обработчика событий из службы "Сетка событий". 

## <a name="service-bus-queues"></a>Очереди служебной шины
События в службе "Сетка событий" можно перенаправлять напрямую в очереди служебной шины для буферизации или использования в сценариях управления и контроля в корпоративных приложениях.

На портале Azure при создании подписки на события выберите **Очередь служебной шины** в качестве типа конечной точки, а затем щелкните **Выбрать конечную точку**, чтобы выбрать очередь служебной шины.

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Добавление обработчика очереди служебной шины с помощью интерфейса командной строки (CLI)

В следующем примере для Azure CLI выполняется подписка на раздел сетки событий и его подключение к очереди служебной шины:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Разделы служебной шины

События в службе "Сетка событий" можно перенаправлять непосредственно в разделы служебной шины для обработки системных событий Azure с помощью разделов служебной шины или для сценариев контроля обмена сообщениями и управления им.

На портале Azure при создании подписки на события выберите **Раздел служебной шины** в качестве типа конечной точки, а затем щелкните **Выбрать конечную точку**, чтобы выбрать раздел служебной шины.

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Добавление обработчика раздела служебной шины с помощью CLI

В следующем примере для Azure CLI выполняется подписка на раздел сетки событий и его подключение к очереди служебной шины:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="message-properties"></a>Свойства сообщений
Если вы используете **раздел или очередь служебной шины** в качестве обработчика событий службы "Сетка событий", задайте следующие заголовки сообщений: 

| Имя свойства | Описание |
| ------------- | ----------- | 
| aeg-subscription-name | Имя подписки на события. |
| aeg-delivery-count | <p>Число попыток, выполненных для события.</p> <p>Пример 1</p> |
| aeg-event-type | <p>Тип события.</p><p> Пример Microsoft.Storage.blobCreated</p> | 
| aeg-metadata-version | <p>Версия метаданных события.</p> <p>Пример 1.</p><p> Для **схемы событий Сетки событий** это свойство представляет версию метаданных, а для **схемы событий облака **—** версию спецификации**. </p>|
| aeg-data-version | <p>Версия данных события.</p><p>Пример 1.</p><p>Для **схемы событий Сетки событий** это свойство представляет версию данных, а для **схемы событий облака** оно не используется.</p> |

## <a name="message-headers"></a>Заголовки сообщений
При отправке события в очередь или раздел служебной шины в виде сообщения в брокере, свойство `messageid` такого сообщения является **идентификатором события**.

Идентификатор события останется неизменным в случае повторной доставки события, чтобы вы могли избежать дублирования доставок, включив **поиск повторяющихся данных** в сущности служебной шины. Рекомендуется установить длительность поиска повторяющихся данных в сущности служебной шины равной сроку жизни события или максимальному периоду времени между повторами, в зависимости от того, какое из этих значений больше.

## <a name="rest-examples-for-put"></a>Примеры REST (для PUT)

### <a name="service-bus-queue"></a>Очередь служебной шины

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>Очередь служебной шины — доставка с использованием управляемого удостоверения

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
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>Раздел служебной шины

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>Раздел служебной шины — доставка с использованием управляемого удостоверения

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
См. список поддерживаемых обработчиков событий в статье [Обработчики событий](event-handlers.md). 
