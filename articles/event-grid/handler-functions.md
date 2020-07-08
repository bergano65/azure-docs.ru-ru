---
title: Использование функции Azure как обработчика событий Сетки событий Azure
description: Узнайте, как можно использовать функции Azure в качестве обработчиков событий Сетки событий Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0152a9fc11562744f83ab9d20466a3dcc8e2e6e0
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800416"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Использование функции Azure как обработчика событий Сетки событий

Обработчик событий — это место, куда отправляются события. Обработчик выполняет действие для обработки события. Некоторые службы Azure автоматически настроены для обработки событий. Одна из них — это **Функции Azure**. 

Используйте **Функции Azure** в бессерверной архитектуре для реагирования на события из Сетки событий. При использовании Функций Azure в качестве обработчика используйте триггер Сетки событий вместо универсальных триггеров HTTP. Сетка событий автоматически проверяет триггеры Сетки событий. При использовании универсальных триггеров HTTP вам нужно самостоятельно реализовать [ответ проверки](webhook-event-delivery.md).

См. сведения в статье [Триггер Сетки событий для Функций Azure](../azure-functions/functions-bindings-event-grid.md).

## <a name="tutorials"></a>Учебники

|Title  |Описание  |
|---------|---------|
| [Краткое руководство. Обработка событий с помощью функции](custom-event-to-function.md) | Отправка пользовательского события в функцию для обработки. |
| [Руководство. Автоматическое изменение размера переданных изображений с помощью службы "Сетка событий"](resize-images-on-storage-blob-upload-event.md) | Пользователи отправляют изображения в учетную запись хранения через веб-приложение. При создании большого двоичного объекта в хранилище служба "Сетка событий" отправляет событие приложению-функции, которое изменяет размер отправленного изображения. |
| [Потоковая передача больших данных в хранилище данных](event-grid-event-hubs-integration.md) | Когда Центры событий создают файл сбора, служба "Сетка событий" отправляет событие приложению-функции. Это приложение извлекает файл сбора и переносит данные в хранилище данных. |
| [Руководство. с примерами интеграции служебной шины Azure со службой "Сетка событий Azure"](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Сетка событий отправляет сообщения из раздела Служебной шины в приложение-функцию и приложение логики. |

## <a name="rest-example-for-put"></a>Пример REST (для PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
См. список поддерживаемых обработчиков событий в статье [Обработчики событий](event-handlers.md). 
