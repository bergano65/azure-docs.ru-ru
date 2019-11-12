---
title: Отображение сведений о компоненте в пакет SDK для Android Azure Maps | Документация Майкрософт
description: Сведения о том, как отображать сведения о компонентах в пакет SDK для Android Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 0dcabb0e5141a92394f2be38cbe7e71fa6e03d58
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928540"
---
# <a name="display-feature-information"></a>Отображение сведений о компоненте

Пространственные данные часто представляются с помощью точек, линий и многоугольников. С этими данными часто связаны метаданные. Например, точка может представлять местоположение магазина, а метаданные об этом ресторане могут быть именем, адресом и типом пищи, который он обслуживает. Эти метаданные можно добавить в качестве свойств этих функций с помощью `JsonObject`. Следующий код создает простую функцию Point со свойством `title`, имеющим значение "Hello World!"

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Create a point feature with some properties and add it to the data source.
JsonObject properties = new JsonObject();
properties.addProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64), properties));
```

Когда пользователь взаимодействует с компонентом на карте, события могут использоваться для реагирования на эти действия. Распространенным сценарием является отображение сообщения, сопоставленного со свойствами метаданных функции, с которой взаимодействует пользователь. Событие `OnFeatureClick` — это основное событие, используемое для определения того, когда пользователь нажал на карту. Существует также `OnLongFeatureClick` событие. При добавлении события `OnFeatureClick` на карту его можно ограничить одним слоем, передав идентификатор слоя для ограничения. Если идентификатор слоя не передается, то при нажатии любой функции на карте, независимо от того, в каком слое он находится, будет срабатывать это событие. Следующий код создает слой символов для отрисовки данных точек на карте, затем добавляет событие `OnFeatureClick` и ограничивает его на этот уровень символов.

```java
//Create a symbol and add it to the map.
SymbolLayer symbolLayer = new SymbolLayer(dataSource);
map.layers.add(symbolLayer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).properties().get("title").getAsString();

    //Do something with the message.
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Отображение всплывающего сообщения

Всплывающее сообщение — это один из самых простых способов отобразить сведения для пользователя и доступен во всех версиях Android. Он не поддерживает никаких типов вводимых пользователем данных и отображается только в течение короткого периода времени. Если вы хотите быстро предоставить пользователю сведения о том, что они применяют, то может быть хорошим вариантом. В следующем коде показано, как можно использовать всплывающее сообщение с событием `OnFeatureClick`.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

<center>

![анимация касания функции и отображаемое всплывающее сообщение](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

Помимо всплывающих сообщений существует много других способов представления свойств метаданных функции, например:

- [Мини](https://developer.android.com/training/snackbar/showing.html) -приложение Снакбар — снаккбарс предоставляет упрощенную обратную связь над операцией. Они показывают короткое сообщение в нижней части экрана на мобильных устройствах, а в нижнем левом углу — на большем. Снаккбарс отображается над всеми другими элементами на экране, и только один из них может отображаться за раз.
- [Диалоговые окна](https://developer.android.com/guide/topics/ui/dialogs) — это небольшое окно, предлагающее пользователю принять решение или ввести дополнительные сведения. Диалоговое окно не заполняет экран и обычно используется для модальных событий, требующих от пользователя выполнения действия, прежде чем они смогут продолжить работу.
- Добавление [фрагмента](https://developer.android.com/guide/components/fragments) к текущему действию.
- Переход к другому действию или представлению.

## <a name="next-steps"></a>Дополнительная информация

Чтобы добавить дополнительные данные на карту, выполните следующие действия.

> [!div class="nextstepaction"]
> [Добавление слоя символов](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Добавление фигур на карту Android](how-to-add-shapes-to-android-map.md)
