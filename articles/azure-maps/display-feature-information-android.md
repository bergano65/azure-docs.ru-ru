---
title: Отображение сведений о функциях в картах Android | Карты Microsoft Azure
description: Сведения о том, как отображать информацию, когда пользователи взаимодействуют с функциями карт. Используйте пакет SDK для Android Azure Maps для вывода всплывающих сообщений и других типов сообщений.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 37b5ab1c144ed81d995da40b87edeaccdcad7253
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679999"
---
# <a name="display-feature-information"></a>Отображение сведений о компоненте

Пространственные данные часто представляются с помощью точек, линий и многоугольников. С этими данными часто связаны метаданные. Например, точка может представлять местоположение ресторана, а метаданные об этом ресторане могут быть именем, адресом и типом пищи, который он обслуживает. Эти метаданные можно добавить как свойства геоjson `Feature` . Следующий код создает простую функцию Point со `title` свойством, имеющим значение "Hello World!"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

Способы создания и добавления данных на карту см. в документации по [созданию источника данных](create-data-source-android-sdk.md) .

Когда пользователь взаимодействует с компонентом на карте, события могут использоваться для реагирования на эти действия. Распространенным сценарием является отображение сообщения, сопоставленного со свойствами метаданных функции, с которой взаимодействует пользователь. `OnFeatureClick`Событие является основным событием, используемым для определения того, когда пользователь нажал на карту. Существует также `OnLongFeatureClick` событие. При добавлении `OnFeatureClick` события к сопоставлению его можно ограничить одним слоем, ПЕРЕДАВ идентификатор слоя для ограничения. Если идентификатор слоя не передается, то при нажатии любой функции на карте, независимо от того, в каком слое он находится, будет срабатывать это событие. Следующий код создает слой символов для отрисовки данных точек на карте, затем добавляет `OnFeatureClick` событие и ограничивает его на этот уровень символов.

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.
}, layer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Отображение всплывающего сообщения

Всплывающее сообщение — это один из самых простых способов отобразить сведения для пользователя и доступен во всех версиях Android. Он не поддерживает никаких типов вводимых пользователем данных и отображается только в течение короткого периода времени. Если вы хотите быстро предоставить пользователю сведения о том, что они применяют, то может быть хорошим вариантом. В следующем коде показано, как можно использовать всплывающее сообщение с `OnFeatureClick` событием.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, layer.getId());    //Limit this event to the symbol layer.
```

![Анимация касания функции и отображение всплывающего сообщения](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)

Помимо всплывающих сообщений существует много других способов представления свойств метаданных функции, например:

- [](https://developer.android.com/training/snackbar/showing.html)  -  Мини `Snackbars` -приложение снаккбар Предоставьте упрощенную обратную связь над операцией. Они показывают короткое сообщение в нижней части экрана на мобильных устройствах, а в нижнем левом углу — на большем. `Snackbars` отображается над всеми другими элементами на экране, и только один может отображаться за раз.
- [Диалоговые окна](https://developer.android.com/guide/topics/ui/dialogs) — это небольшое окно, предлагающее пользователю принять решение или ввести дополнительные сведения. Диалоговое окно не заполняет экран и обычно используется для модальных событий, требующих от пользователя выполнения действия, прежде чем они смогут продолжить работу.
- Добавление [фрагмента](https://developer.android.com/guide/components/fragments) к текущему действию.
- Переход к другому действию или представлению.

## <a name="next-steps"></a>Следующие шаги

Чтобы добавить дополнительные данные на карту, выполните следующие действия.

> [!div class="nextstepaction"]
> [Реагирование на события Map](android-map-events.md)

> [!div class="nextstepaction"]
> [Создание источника данных](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Добавление слоя символов](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Добавление слоя линий](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя многоугольников](how-to-add-shapes-to-android-map.md)
