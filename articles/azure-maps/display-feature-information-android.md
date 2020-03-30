---
title: Отображение информации о функциях в Android SDK Azure Maps Карты Microsoft Azure
description: В этой статье вы узнаете, как отображать информацию о функциях на карте с помощью SDK Android SDK Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 26f41a7fd88a3c2018592e89ae95e3b962c1a9e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911694"
---
# <a name="display-feature-information"></a>Отображение сведений о компоненте

Пространственные данные часто представлены с помощью точек, линий и полигонов. Эти данные часто имеют информацию о метаданных, связанную с ними. Например, точка может представлять местоположение магазина и метаданные о том, что ресторан может быть его имя, адрес и тип пищи он служит. Эти метаданные могут быть добавлены в `JsonObject`качестве свойств этих функций с помощью . Следующий код создает простую `title` точку функцию с свойством, которое имеет значение "Hello World!"

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

Когда пользователь взаимодействует с функцией на карте, события могут быть использованы для реагирования на эти действия. Распространенным сценарием является отображение сообщения, сделанного из свойств метаданных объекта, с которым взаимодействовал пользователь. Событие `OnFeatureClick` является главным событием, используемым для обнаружения, когда пользователь постучал функцию на карте. Там также `OnLongFeatureClick` событие. При добавлении `OnFeatureClick` события на карту его можно ограничить одним слоем, передавая в идентификатор слоя, чтобы ограничить его. Если идентификатор слоя не передается, нажатие любой функции на карте, независимо от того, в каком слое он находится, будет стрелять это событие. Следующий код создает слой символа для визуализации точечных данных на карте, затем добавляет `OnFeatureClick` событие и ограничивает его этим слоем символа.

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

## <a name="display-a-toast-message"></a>Отобразить сообщение всплывающих тостов

Сообщение toast является одним из самых простых способов отображения информации для пользователя и доступно во всех версиях Android. Он не поддерживает любой тип пользовательского ввода и отображается только в течение короткого периода времени. Если вы хотите быстро сообщить пользователю что-то о том, что они постучали на, тост сообщение может быть хорошим вариантом. Следующий код показывает, как сообщение всплывающих тостов может быть использовано с событием. `OnFeatureClick`

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

![Анимация функции, которую можно использовать, и отображаемый всплывающее сообщение](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

Помимо всплывающих сообщений, существует множество других способов представления свойств метаданных объекта, таких как:

- [Виджет Snakbar](https://developer.android.com/training/snackbar/showing.html) - Snackbars обеспечивают легкую обратную связь об операции. Они показывают краткое сообщение в нижней части экрана на мобильных устройствах и в левом нижнем углу на больших устройствах. Snackbars появляются выше всех других элементов на экране, и только один может отображаться одновременно.
- [Диалоги](https://developer.android.com/guide/topics/ui/dialogs) - Диалог — это небольшое окно, которое побуждает пользователя принять решение или ввести дополнительную информацию. Диалог не заполняет экран и обычно используется для модальных событий, которые требуют от пользователей принять меры, прежде чем они смогут продолжить.
- Добавьте [фрагмент](https://developer.android.com/guide/components/fragments) в текущее действие.
- Перейдите к другому действию или представлению.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы добавить больше данных на карту:

> [!div class="nextstepaction"]
> [Добавление слоя символов](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Добавление фигур на карте Android](how-to-add-shapes-to-android-map.md)
