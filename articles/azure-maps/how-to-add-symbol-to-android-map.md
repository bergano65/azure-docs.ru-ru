---
title: Добавление слоя символов в карты Android | Карты Microsoft Azure
description: Узнайте, как добавить маркер к карте. См. пример, использующий Azure Maps пакет SDK для Android для добавления слоя символов, который содержит данные на основе точек из источника данных.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 040fcde35707074ffaf102ed6c224b2f47a084bb
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679336"
---
# <a name="add-a-symbol-layer-android-sdk"></a>Добавление слоя символов (пакет SDK для Android)

В этой статье показано, как преобразовать данные точек из источника данных в качестве слоя символов на карте с помощью Azure Maps пакет SDK для Android. Слои символов отрисовывает точки в виде изображения и текста на карте.

> [!TIP]
> Слои символов по умолчанию отображают координаты всех геометрических объектов в источнике данных. Чтобы ограничить слой таким образом, чтобы он отображал только возможности геометрических точек, установите `filter` параметр слоя в значение `eq(geometryType(), "Point")` . Если вы хотите включить компоненты MultiPoint, установите `filter` параметр слоя в значение `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

## <a name="prerequisites"></a>Предварительные требования

Не забудьте выполнить действия, описанные в разделе [Краткое руководство. Создание документа приложения Android](quick-android-map.md) . Блоки кода в этой статье можно вставить в `onReady` обработчик событий Maps.

## <a name="add-a-symbol-layer"></a>Добавление слоя символов

Прежде чем добавить на карту слой символов, необходимо выполнить несколько действий. Сначала создайте источник данных и добавьте его на карту. Создание слоя символов. Затем передайте источник данных на слой символов, чтобы получить данные из источника данных. Наконец, добавьте данные в источник данных, чтобы отобразить что-либо.

В приведенном ниже коде показано, что следует добавить к карте после ее загрузки. Этот пример отображает единую точку на карте с помощью слоя символов.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

Существует три различных типа данных точек, которые можно добавить на карту:

- Геометрическая точка в формате JSON. Этот объект содержит только координаты точки и ничего другого. `Point.fromLngLat`Статический метод можно использовать для простого создания этих объектов.
- Геометрическая геометрия MultiPoint — этот объект содержит координаты нескольких точек и ничего другого. Передайте массив точек в `MultiPoint` класс для создания этих объектов.
- Функция геоjson — этот объект состоит из любой геометрической фигуры и набора свойств, содержащих метаданные, связанные с геометрическим объектом.

Дополнительные сведения см. в документе [Создание источника данных](create-data-source-android-sdk.md) для создания и добавления данных на карту.

В следующем примере кода создается геометрическая точка в формате JSON, которая передается в функцию геоjson и имеет `title` значение, добавленное к его свойствам. `title`Свойство отображается как текст над значком символа на карте.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

На следующем снимке экрана показан приведенный выше код, отрисовки компонент с помощью значка и текстовой метки с уровнем символов.

![Сопоставьте с точкой, отображаемой с помощью слоя символов, отображающего значок и текстовую метку для функции точки](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> По умолчанию слои символов оптимизируют отрисовку символов путем скрытия перекрывающихся символов. При изменении масштаба скрытые символы становятся видимыми. Чтобы отключить эту функцию и вывести все символы в любое время, задайте `iconAllowOverlap` `textAllowOverlap` для параметров и значение `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Добавление пользовательского значка в слой символов

Слои символов преобразовываются для просмотра с помощью WebGL. Таким образом, все ресурсы (например, образы значков) необходимо загрузить в контекст WebGL. В этом примере показано, как добавить пользовательский значок в ресурсы Map. Этот значок используется для отрисовки данных точек с помощью пользовательского символа на карте. Свойство `textField` слоя символа требует указания выражения. В этом случае нам нужно отобразить свойство температуры. Так как температура является числом, ее необходимо преобразовать в строку. Кроме того, мы хотим добавить к нему «° F». Для этого объединения можно использовать выражение. `concat(Expression.toString(get("temperature")), literal("°F"))`.

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

В этом примере приведенное ниже изображение было загружено в папку для рисования приложения.

| ![Изображение значка погоды для дождя ливни](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

На следующем снимке экрана показан приведенный выше код, отрисовки компонент с помощью пользовательского значка и метки форматированного текста с слоем символов.

![Сопоставьте с точкой, отображаемой с помощью слоя символов, отображающего пользовательский значок и метку форматированного текста для функции точки](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> Если требуется отобразить только текст с помощью слоя символов, можно скрыть значок, задав `iconImage` для свойства параметров значка значение `"none"` .

## <a name="next-steps"></a>Следующие шаги

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Создание источника данных](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Использование стилистических выражений на основе данных](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Отображение сведений о компоненте](display-feature-information-android.md)
