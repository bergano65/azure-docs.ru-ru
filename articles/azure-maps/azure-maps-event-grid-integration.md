---
title: Реагируйте на события карты с помощью Event Grid Карты Microsoft Azure
description: В этой статье вы узнаете, как реагировать на события Microsoft Azure Maps с помощью Event Grid.
author: philmea
ms.author: philmea
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9c9483af191e5439af0c0b5e433187d6475c178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335718"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Реагирование на события Azure Maps c помощью Сетки событий 

Azure Maps интегрируется с Azure Event Grid, чтобы пользователи могли отправлять уведомления о событиях в другие службы и запускать процессы ниже по течению. Цель этой статьи — помочь настроить бизнес-приложения для прослушивания событий Azure Maps. Это позволяет пользователям реагировать на критические события надежным, масштабируемым и безопасным способом. Например, пользователи могут создать приложение для обновления базы данных, создания билета и доставки уведомления по электронной почте каждый раз, когда устройство входит в геоограждение.

Azure Event Grid — это полностью управляемая служба разгрома событий, использующая модель подписки на публикацию. Event Grid имеет встроенную поддержку служб Azure, таких как [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) и [Azure Logic Apps.](https://docs.microsoft.com/azure/azure-functions/functions-overview) Он может предоставлять оповещения о событиях службам, не являющихся Azure, с помощью веб-крючков. Полный список обработчиков событий, которые поддерживает служба "Сетка событий", см. в статье [An introduction to Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) (Общие сведения о службе "Сетка событий Azure").


![Функциональная модель Сетки событий Azure](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Типы событий Azure Maps

Сетка событий использует [подписки на события](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) для маршрутизации сообщений о событиях подписчикам. Учетная запись Azure Maps выводит следующие типы событий. 

| Тип события | Описание |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Поднятые при получении координат переместились из-за пределовданного геозабора в |
| Microsoft.Maps.GeofenceExited | Поднятые при получении координат переместились из заданного геозабора на внешний |
| Microsoft.Maps.GeofenceResult | Создается каждый раз, когда запрос геозоны возвращает результат с любым состоянием. |

## <a name="event-schema"></a>Схема событий

Следующий пример показывает схему для GeofenceResult:

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="tips-for-consuming-events"></a>Советы по потреблению событий

Приложения, которые обрабатывают события геозоны Azure Maps, должны соответствовать нескольким рекомендациям.

* Нанастройка нескольких подписок для маршрутизатока событий в одно и то же обработчик событий. Не следует предполагать, что события поступают из определенного источника. Всегда проверяйте тему сообщения, чтобы убедиться, что сообщение пришло из источника, который вы ожидаете.
* Используйте `X-Correlation-id` поле в заголовке ответов, чтобы понять, актуальна ли ваша информация об объектах. Сообщения могут прибывать не по порядку или с задержкой.
* Когда запрос GET или POST в API Geofence вызывается `EnterAndExit`с параметром режима, установленным для, то событие Enter or Exit генерируется для каждой геометрии в геозаборе, для которого статус изменился по сравнению с предыдущим вызовом Geofence API.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о том, как использовать геозоны для операций управления на сайте конструкции, см. в статье

> [!div class="nextstepaction"] 
> [Настройка геозоны с использованием Azure Maps](tutorial-geofence.md)
