---
title: Формат данных GeoJSON для геозабора Карты Microsoft Azure
description: В этой статье вы узнаете о том, как подготовить данные о геозаборах, которые могут быть использованы в Microsoft Azure Maps GET и POST Geofence API.
author: philmea
ms.author: philmea
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7b9860908dd3bdf3dcda727f350578a97b890cac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335621"
---
# <a name="geofencing-geojson-data"></a>Данные GeoJSON для геозон

В Azure Maps API-интерфейсы [GET Geofence](/rest/api/maps/spatial/getgeofence) и [POST Geofence](/rest/api/maps/spatial/postgeofence) позволяют получить сведения о близости координат относительно предоставленных геозон или набора границ. В этой статье подробно описано, как подготовить данные для геозон, применимые в API-интерфейсах GET и POST в Azure Maps.

Данные для геозон или набор геозон представляются объектами `Feature` и `FeatureCollection` в формате `GeoJSON`, который определен в стандарте [RFC7946](https://tools.ietf.org/html/rfc7946). В дополнение к этому:

* Объект GeoJSON может иметь тип объекта `Feature` или `FeatureCollection`.
* Объект Geometry может иметь тип `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon` или `GeometryCollection`.
* Все свойства компонента должны содержать атрибут `geometryId`, который используется для идентификации геозоны.
* Компонент с типом `Point`, `MultiPoint`, `LineString` и `MultiLineString` должен содержать `radius` в списке своих свойств. Значение `radius` измеряется в метрах. Для `radius` допускаются значения в диапазоне от 1 до 10000.
* Компонент с типом геометрии `polygon` и `multipolygon` не имеет свойства radius.
* Используя необязательное свойство `validityTime`, пользователь может настроить время ожидания и период действия для данных геозон. Если это значение не указано, срок данных никогда не истечет и они всегда считаются действительными.
* `expiredTime` обозначает дату и время истечения срока действия для данных геозон. Если в запросе указано более позднее значение `userTime`, чем значение этого свойства, соответствующие данные геозон считаются устаревшими и не включаются в запросы. При запросе идентификатор geometryId этих данных геозон включается в массив `expiredGeofenceGeometryId` в ответе о геозонах.
* `validityPeriod` содержит список периодов действия для геозон. Если значение `userTime` в запросе не попадает в период действия, соответствующие данные геозоны считаются недопустимыми и не включаются в запрос. Идентификатор geometryId этих данных геозон включается в массив `invalidPeriodGeofenceGeometryId` в ответе о геозонах. В следующей таблице приведены свойства для элемента validityPeriod.

| name | Тип | Обязательно  | Описание |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime  | Да | Дата и время начала периода действия. |
| endTime   | Datetime  | Да |  Дата и время окончания периода действия. |
| recurrenceType | строка | false |   Тип повторений для периода. Возможны следующие значения: `Daily`, `Weekly`, `Monthly` или `Yearly`. Значение по умолчанию: `Daily`.|
| businessDayOnly | Логическое | false |  Указывает, что данные считаются допустимыми только в рабочие дни. Значение по умолчанию: `false`.|


* Все значения координат представлены как «долгота, широта», определяемые в `WGS84`.
* Для каждого компонента, который содержит `MultiPoint`, `MultiLineString`, `MultiPolygon` или `GeometryCollection`, свойства применяются ко всем элементам. например: Все точки `MultiPoint` в будет использовать тот же радиус, чтобы сформировать несколько кругов оговора.
* Если используются точки, определяющие круги, геометрию этих кругов можно представить с помощью геометрического объекта `Point`, свойства которого описаны в статье [о расширении геометрических объектов GeoJSON](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Ниже приведен примерный текст запроса по геозоне, которая представлена в виде набора кругов, определенных в `GeoJSON` по координатам центральной точки и радиусу. Период действия этой геозоны: 9:00 до 17:00 по рабочим дням, начиная с 22 октября 2018 г. `expiredTime` обозначает, что данные этой геозоны будут считаться истекшими, если значение `userTime` в запросе позднее, чем `2019-01-01`.  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```
