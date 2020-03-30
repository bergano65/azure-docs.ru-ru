---
title: Добавьте на карту слой экструзии полигона Карты Microsoft Azure
description: Как добавить слой экструзии полигона в веб-SDK Microsoft Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7405098bd4924333aafcd1c285eb2f37bb1d4f75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334547"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Добавьте на карту слой экструзии полигона

В этой статье показано, как использовать слой экструзии `Polygon` `MultiPolygon` полигона для визуализации областей и характеристик геометрии в виде экструдированных фигур. Веб-SDK Azure Maps поддерживает визуализацию геометрии Круга, как это определено в [расширенной схеме GeoJSON.](extend-geojson.md#circle) Эти круги могут быть преобразованы в полигоны при отображении на карте. Все функции геометрии могут быть легко обновлены при завернутый с [атласом. Класс формы.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)

## <a name="use-a-polygon-extrusion-layer"></a>Используйте слой экструзии полигона

Подключите [слой экструзии полигона](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) к источнику данных. Затем загрузил его на карту. Слой экструзии полигона отображает области `Polygon` и `MultiPolygon` функции в виде экструдированных форм. Свойства `height` `base` и свойства полигонного экструзионного слоя определяют базовое расстояние от земли и высоту экструдированной формы в **метрах.** Следующий код показывает, как создать полигон, добавить его в источник данных и сделать его с помощью класса экструзионного слоя Полигона.

> [!Note]
> Значение, `base` определяемое в слой экструзии полигона, `height`должно быть меньше или равно значению.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Экструдированный полигон" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>перо Экструдированный полигон</a> Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps ( ) на <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Добавление полигонов, управляемых данными

Карта хоросплавов может быть отрисована с помощью слоя экструзии полигона. Установите `height` `fillColor` и свойства экструзионного слоя для измерения `Polygon` статистической переменной в геометрии и `MultiPolygon` характеристиках. Следующий пример кода показывает экструдированную хоролетную карту США, основанную на измерении плотности населения по штату.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Экструдированная карта хороплета" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите карту Pen <a href='https://codepen.io/azuremaps/pen/eYYYNox'>Extruded choropleth</a> по<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Добавление окружности на карту

Azure Maps использует расширенную версию схемы GeoJSON, которая содержит определение для кругов, как указано [здесь.](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle) Экструдированный круг можно отобразить на `point` карте, `subType` создав функцию с свойством `Circle` и пронумерованное `Radius` свойство, представляющее радиус в **метрах.** Пример:

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure Maps Web SDK `Point` преобразует `Polygon` эти функции в функции под капотом. Эти `Point` функции могут быть отрисованы на карте с помощью многогонных экструзионных слоев, как показано в следующем образце кода.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Полигон воздушного пространства дронов" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>полигна воздушного пространства</a> Pen<a href='https://codepen.io/azuremaps'>@azuremaps</a>Drone по Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Настройка слоя экструзии полигона

Слой Polygon Extrusion имеет несколько вариантов укладки. которые можно опробовать с помощью представленного ниже средства.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Смотрите Pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Полигона](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [слой экструзии полигона](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Дополнительные ресурсы:

> [!div class="nextstepaction"]
> [Расширение спецификации Azure Maps GeoJSON](extend-geojson.md#circle)
