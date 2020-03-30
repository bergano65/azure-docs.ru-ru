---
title: Добавить слой символа на карту Карты Microsoft Azure
description: В этой статье вы узнаете о том, как использовать слой символа для настройки символа, и добавить символы на карте с помощью веб-SDK Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b8d131dcc798fb2fe1d4bb650cd5b0a68903381b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209704"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Добавление слоя символов на карту

Подключите символ к источнику данных и используйте его для визуализации значка или текста в заданном моменте. 

Слои символов преобразовываются для просмотра с помощью WebGL. Используйте слой символов для визуализации больших коллекций точек на карте. По сравнению с HTML-маркером, слой символов отображает большое количество точечных данных на карте с более высокой производительностью. Однако слой символа не поддерживает традиционные csS и HTML-элементы для укладки.  

> [!TIP]
> Слои символов по умолчанию отображают координаты всех геометрических объектов в источнике данных. Чтобы ограничить слой таким, чтобы он отображал только объекты геометрии точек, устанавливающие `filter` свойство слоя `['==', ['geometry-type'], 'Point']` или `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` если хотите, можно также включить функции MultiPoint.

Менеджер спорит изображения карт загружает пользовательские изображения, используемые слоем символа. Он поддерживает следующие форматы изображений:

- JPEG
- PNG
- SVG
- BMP
- GIF (без анимации)

## <a name="add-a-symbol-layer"></a>Добавление слоя символов

Прежде чем добавить на карту слой символов, необходимо сделать несколько шагов. Во-первых, создайте источник данных и добавьте его на карту. Создайте слой символов. Затем перейдите в источник данных на слой символа, чтобы получить данные из источника данных. Наконец, добавьте данные в источник данных, чтобы было что-то отображено. 

Приведенный ниже код демонстрирует, что следует добавить на карту после загрузки. Этот образец отображает одну точку на карте с помощью слоя символа. 

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

Есть четыре различных типа точечных данных, которые могут быть добавлены на карту:

- Геометрия Гео-СОН Пойнт - Этот объект содержит только координаты точки и ничего больше. Класс `atlas.data.Point` помощника можно легко использовать для создания этих объектов.
- Геометрия GeoJSON MultiPoint - Этот объект содержит координаты нескольких точек и ничего больше. Класс `atlas.data.MultiPoint` помощника можно легко использовать для создания этих объектов.
- Функция GeoJSON - Этот объект состоит из любой геометрии GeoJSON и набора свойств, содержащих метаданные, связанные с геометрией. Класс `atlas.data.Feature` помощника можно легко использовать для создания этих объектов.
- `atlas.Shape`класс похож на функцию GeoJSON. Оба они состоят из геометрии GeoJSON и набора свойств, содержащих метаданные, связанные с геометрией. Если объект GeoJSON добавляется в источник данных, его можно легко визуализировать в слое. Однако, если свойства координат этого объекта GeoJSON обновляются, источник данных и карта не изменяются. Это потому, что в объекте JSON нет механизма для запуска обновления. Класс формы предоставляет функции для обновления данных, которые он содержит. При внесении изменений источник данных и карта автоматически уведомляются и обновляются. 

Следующий пример кода создает геометрию GeoJSON Point `atlas.Shape` и передает ее в класс, чтобы упростить обновление. Центр карты первоначально используется для визуализации символа. Событие щелчка добавляется на карту таким образом, что при пожаре координаты мыши используются с `setCoordinates` функцией форм. Координаты мыши регистрируются во время события щелчка. Затем `setCoordinates` обновляется расположение символа на карте.

<br/>

<iframe height='500' scrolling='no' title='Переключение расположения маркера' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Переключение расположения маркера</a> от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> По умолчанию слои символов оптимизируют визуализацию символов, скрывая символы, которые перекрываются. При увеличении скрытых символов становятся видимыми. Чтобы отключить эту функцию и вставить все `allowOverlap` символы `iconOptions` в `true`любое время, установите свойство опций.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Добавление пользовательского значка в слой символов

Слои символов преобразовываются для просмотра с помощью WebGL. Таким образом, все ресурсы (например, образы значков) необходимо загрузить в контекст WebGL. В этом примере показано, как добавить пользовательский значок к ресурсам карты. Этот значок затем используется для визуализации точечных данных с пользовательским символом на карте. Свойство `textField` слоя символа требует указания выражения. В этом случае мы хотим сделать свойство температуры. Так как температура является числом, она должна быть преобразована в строку. Кроме того, мы хотим придать ему "ЗФ". Выражение может быть использовано для этого конкатенации; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Значок изображения пользовательских символов' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. ручку <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Custom Symbol Image Icon</a> (Значок изображения пользовательских символов) от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Веб-SDK Azure Maps предоставляет несколько настраиваемых шаблонов изображений, которые можно использовать со слоем символов. Для получения дополнительной информации смотрите документ [«Как использовать шаблоны изображений».](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-symbol-layer"></a>Настройка слоя символа 

Слой символа имеет множество доступных вариантов стилизации. Вот средство для проверки этих различных параметров.

<br/>

<iframe height='700' scrolling='no' title='Параметры слоя символов' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите ручку <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Symbol Layer Options</a> (Параметры слоя символов) от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Если вы хотите визуализировать только текст со слоем `image` символа, вы `'none'`можете скрыть значок, установив свойство параметров значка.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [SymbolLayer class](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) (Класс SymbolLayer)

> [!div class="nextstepaction"]
> [SymbolLayerOptions interface](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) (Интерфейс SymbolLayerOptions)

> [!div class="nextstepaction"]
> [IconOptions interface](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest) (Интерфейс IconOptions)

> [!div class="nextstepaction"]
> [Текстовые варианты](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Создание источника данных](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Добавление всплывающего окна](map-add-popup.md)

> [!div class="nextstepaction"]
> [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Использование шаблонов образов](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Добавление слоя линий](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя многоугольников](map-add-shape.md)

> [!div class="nextstepaction"]
> [Добавить слой пузыря](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков на карту](map-add-bubble-layer.md)
