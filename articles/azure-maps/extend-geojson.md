---
title: Расширенные геометрии GeoJSON Карты Microsoft Azure
description: В этой статье вы узнаете о том, как Microsoft Azure Maps расширяет спецификацию GeoJSON для представления определенных геометрий.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 98db10f0fc7a417f39d4bb00e77af6bdea034a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276404"
---
# <a name="extended-geojson-geometries"></a>Расширенные геометрии GeoJSON

Azure Maps предоставляет список мощных API для поиска внутри и вдоль географических объектов. Эти AA придерживаются стандартных [спецификаций GeoJSON,][1] представляющих географические особенности.  

Спецификация [GeoJSON][1] поддерживает только следующие геометрии:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Точка
* Многоугольник

Некоторые API-аДИ Azure Maps принимают геометрии, которые не являются частью [спецификации GeoJSON.][1] Например, API [search Inside Geometry](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) принимает Круг и полигоны.

В этой статье приводится подробное объяснение того, как Azure Maps расширяет [спецификацию GeoJSON][1] для представления определенных геометрических объектов.

## <a name="circle"></a>Circle

Геометрия `Circle` не поддерживается [спецификацией GeoJSON.][1] Мы используем `GeoJSON Point Feature` объект для представления круга.

Геометрия, `Circle` представленная `GeoJSON Feature` с помощью объекта, __должна__ содержать следующие координаты и свойства:

- Центр.

    Центр круга представлен с помощью `GeoJSON Point` объекта.

- Радиус.

    Значение `radius` круга представлено с помощью свойств `GeoJSON Feature`. Значение радиуса отображается в _метрах_ и должно быть типа `double`.

- Подтип

    Геометрический объект "круг" также должен содержать свойство `subType`. Это свойство должно быть `GeoJSON Feature`частью свойств 's и его значение должно быть _Круг_

#### <a name="example"></a>Пример

Вот как вы будете представлять круг `GeoJSON Feature` с помощью объекта. Давайте поцентрим круг на широте: 47.639754 и долготу: -122.126986, и присвоим ему радиус, равный 100 метрам:

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>Прямоугольник

Геометрия `Rectangle` не поддерживается [спецификацией GeoJSON.][1] Мы используем `GeoJSON Polygon Feature` объект для представления прямоугольника. Расширение прямоугольника в основном используется модулем инструментов рисования Web SDK.

Геометрия, `Rectangle` представленная `GeoJSON Polygon Feature` с помощью объекта, __должна__ содержать следующие координаты и свойства:

- Углы

    Углы прямоугольника представлены с помощью координат `GeoJSON Polygon` объекта. Должно быть пять координат, по одному для каждого угла. И, пятая координата, которая такая же, как первая координата, чтобы закрыть полигажное кольцо. Предполагается, что эти координаты совпадают, и что разработчик может повернуть их по мере запроса.

- Подтип

    Геометрия прямоугольника также должна `subType` содержать свойство. Это свойство должно быть `GeoJSON Feature`частью свойств ', и его значение должно быть _Rectangle_

### <a name="example"></a>Пример

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о данных GeoJSON в Azure Maps:

> [!div class="nextstepaction"]
> [Формат Geofence GeoJSON](geofence-geojson.md)

Просмотрите глоссарий общих технических терминов, связанных с Azure Maps и приложениями для анализа местоположений:

> [!div class="nextstepaction"]
> [Блеск карты Azure](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
