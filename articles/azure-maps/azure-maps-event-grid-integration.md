---
title: Реагирование на события Azure Maps с помощью Сетки событий | Документация Майкрософт
description: Узнайте, как реагировать на события Azure Maps c помощью Сетки событий.
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a70011b934398ac4e7f74bb67013e93bb5e86e4e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60799154"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Реагирование на события Azure Maps c помощью Сетки событий 

Azure Maps интегрируется с Сеткой событий Azure, чтобы вы могли отправлять уведомления о событии в другие службы и активировать нисходящие процессы. Цель этой статьи — помочь вам настроить свои бизнес-приложения для ожидания передачи данных о событиях Azure Maps, чтобы вы могли реагировать на критические события надежным, масштабируемым и безопасным способом. Например, выполните сборку приложения, которое выполняет несколько действий, таких как обновление базы данных, создание запроса и доставление уведомления по электронной почте, каждый раз, когда устройство входит в геозону.

Сетка событий Azure — это полностью управляемая служба маршрутизации событий, использующая модель публикации и подписки. Эта служба поддерживает такие службы Azure, как [Функции Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) и [Azure Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview), и может доставлять оповещения о событиях в службы за пределами Azure с помощью веб-перехватчиков. Полный список обработчиков событий, которые поддерживает служба "Сетка событий", см. в статье [An introduction to Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) (Общие сведения о службе "Сетка событий Azure").


![Функциональная модель Сетки событий Azure](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Типы событий Azure Maps

Сетка событий использует [подписки на события](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) для маршрутизации сообщений о событиях подписчикам. Учетная запись Azure Maps выводит следующие типы событий. 

| Тип события | Описание |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Создается, когда полученные координаты переходят из заданной геозоны наружу. |
| Microsoft.Maps.GeofenceExited | Создается, когда полученные координаты переходят из заданной геозоны наружу |
| Microsoft.Maps.GeofenceResult | Создается каждый раз, когда запрос геозоны возвращает результат с любым состоянием. |

## <a name="event-schema"></a>Схема событий

На следующем примере показана схема для GeofenceResult

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

* Несколько подписок можно настроить для маршрутизации событий в тот же обработчик событий. Не следует предполагать, что события поступают из определенного источника. Всегда проверяйте тему сообщения, чтобы убедиться, что оно поступает из ожидаемого источника.
* Сообщения могут прибывать не по порядку или с задержкой. Используйте поле `X-Correlation-id` в заголовке ответа, чтобы понять, является ли информация об объектах актуальной.
* При вызове API геозоны Get и POST с параметром режима, которому присвоено значение `EnterAndExit`, событие Enter или Exit генерируется для каждого геометрического объекта в геозоне, для которого изменилось состояние из предыдущего вызова API геозоны.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о том, как использовать геозоны для операций управления на сайте конструкции, см. в статье

> [!div class="nextstepaction"] 
> [Настройка геозоны с использованием Azure Maps](tutorial-geofence.md)