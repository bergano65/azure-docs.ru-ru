---
title: Добавление слоя символов в Azure Maps | Документация Майкрософт
description: Добавление символов в веб-пакет SDK для Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 10f6a7ef92bfd6558ed93e7fb40df9e48e1b92f5
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976179"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Добавление слоя символов на карту

Символ может быть подключен к источнику данных и использоваться для отображения значка и (или) текста в заданной точке. Слои символов подготавливаются к просмотру с помощью WebGL и могут использоваться для отображения больших коллекций точек на карте. Этот слой может отображать гораздо больше данных на карте с хорошей производительностью, чем то, что достижимо с помощью маркеров HTML. Однако слой символов не поддерживает традиционные элементы CSS и HTML для стилей.  

> [!TIP]
> Слои символов по умолчанию отображают координаты всех геометрических объектов в источнике данных. Чтобы ограничить слой таким образом, чтобы он отображал только возможности геометрических точек `filter` , задайте для `['==', ['geometry-type'], 'Point']` свойства слоя значение `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` или, если требуется включить компоненты MultiPoint.

## <a name="add-a-symbol-layer"></a>Добавление слоя символов

Чтобы добавить слой символов на карту и отобразить данные, сначала необходимо создать источник данных и добавить карту. Затем можно создать и передать в источнике данных слой символов для получения данных из. Наконец, необходимо добавить данные в источник данных для подготовки к просмотру. В следующем коде показан код, который должен быть добавлен к карте после его загрузки для отрисовки одной точки на карте с помощью слоя символов. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Существует четыре типа данных Point, которые можно добавить на карту:

- Геометрическая точка в формате JSON. Этот объект содержит только координаты точки и ничего другого. `atlas.data.Point` Вспомогательный класс можно использовать для простого создания этих объектов.
- Геометрическая геометрия MultiPoint — этот объект содержит координаты нескольких точек, но ничего другого нет. `atlas.data.MultiPoint` Вспомогательный класс можно использовать для простого создания этих объектов.
- Функция геоjson — этот объект состоит из любой геометрической фигуры и набора свойств, содержащих метаданные, связанные с геометрическим объектом. `atlas.data.Feature` Вспомогательный класс можно использовать для простого создания этих объектов.
- `atlas.Shape`класс похож на функцию геоjson в том, что она состоит из геометрической геометрии и набора свойств, содержащих метаданные, связанные с геометрическим объектом. Если объект геоjson-файла добавляется в источник данных, его можно легко отобразить на слое, однако если свойство координаты этого объекта геообъектно-JSON обновляется, источник данных и схема не изменятся, так как в объекте JSON нет механизма для активации обновления. Класс Shape предоставляет функции для обновления содержащихся в ней данных, а при внесении изменений источник данных и схема автоматически уведомляются и обновляются. 

В следующем примере кода создается геометрическая точка в формате JSON, которая передается `atlas.Shape` в класс, чтобы упростить обновление. Центр схемы изначально используется для отрисовки символа. Событие щелчка добавляется к карте таким, что при его срабатывании координаты, по которым была нажата мышь, используются с функцией Shapes `setCoordinates` , которая обновляет расположение символа на карте.

<br/>

<iframe height='500' scrolling='no' title='Переключение расположения маркера' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Переключение расположения маркера</a> от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> По умолчанию для производительности слои символов оптимизируют отрисовку символов путем скрытия перекрывающихся символов. При изменении масштаба скрытые символы становятся видимыми. Чтобы отключить эту функцию и вывести все символы в любое время, задайте `allowOverlap` для `true`свойства `iconOptions` параметра значение.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Добавление пользовательского значка в слой символов

Слои символов преобразовываются для просмотра с помощью WebGL. Таким образом, все ресурсы (например, образы значков) необходимо загрузить в контекст WebGL. В этом примере показано, как добавить пользовательский значок к ресурсам сопоставлений, а затем использовать его для отрисовки данных точек с помощью пользовательского символа на карте. Свойство `textField` слоя символа требует указания выражения. В этом случае мы хотим отобразить свойство температуры, но поскольку это число, его необходимо преобразовать в строку. Кроме того, мы хотим добавить к нему «° F». Для этого можно использовать выражение. `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Значок изображения пользовательских символов' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. ручку <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Custom Symbol Image Icon</a> (Значок изображения пользовательских символов) от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Azure Maps веб-пакет SDK предоставляет несколько настраиваемых шаблонов изображений, которые можно использовать с уровнем символов. Дополнительные сведения см. в документе [Использование шаблонов изображений](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-symbol-layer"></a>Настройка слоя символа 

Слой символа имеет множество доступных вариантов стилизации. Вот средство для проверки этих различных параметров.

<br/>

<iframe height='700' scrolling='no' title='Параметры слоя символов' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите ручку <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Symbol Layer Options</a> (Параметры слоя символов) от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Если требуется отображать только текст с помощью слоя символов, можно скрыть значок, установив `image` для `'none'`свойства параметров значка значение.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [SymbolLayer class](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) (Класс SymbolLayer)

> [!div class="nextstepaction"]
> [SymbolLayerOptions interface](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) (Интерфейс SymbolLayerOptions)

> [!div class="nextstepaction"]
> [IconOptions interface](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest) (Интерфейс IconOptions)

> [!div class="nextstepaction"]
> [текстоптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Создание источника данных](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Добавление всплывающего окна](map-add-popup.md)

> [!div class="nextstepaction"]
> [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Использование шаблонов изображений](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Добавить слой линий](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя многоугольников](map-add-shape.md)

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков на карту](map-add-bubble-layer.md)
