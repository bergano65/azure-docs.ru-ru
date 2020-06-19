---
title: Гибридное подключение Azure Relay как обработчик событий службы "Сетка событий Azure"
description: В статье показано, как гибридные подключения Azure Relay можно использовать в качестве обработчиков событий службы "Сетка событий Azure".
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 04fbc7b739fa8ea7b08a3341c2f78244c445e721
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800879"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Гибридное подключение Azure Relay как обработчик событий службы "Сетка событий Azure"
Обработчик событий — это место, куда отправляются события. Обработчик выполняет последующую обработку полученного события. Некоторые службы Azure автоматически настроены для обработки событий. Одна из них — это **Azure Relay**. 

**Гибридные подключения Azure Relay** используются для отправки событий в приложения, которые находятся в корпоративной сети и не имеют общедоступной конечной точки.

## <a name="tutorials"></a>Учебники
Пример использования гибридного подключения Azure Relay в качестве обработчика событий см. в следующем руководстве. 

|Title  |Описание  |
|---------|---------|
| [Руководство. Отправка пользовательских событий для службы Сетка событий Azure по гибридному подключению](custom-event-to-hybrid-connection.md) | Отправка пользовательских событий в существующее гибридное подключение для обработки приложением прослушивателя. |

## <a name="rest-example-for-put"></a>Пример REST (для PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
См. список поддерживаемых обработчиков событий в статье [Обработчики событий](event-handlers.md). 