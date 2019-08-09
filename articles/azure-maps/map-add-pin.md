---
title: Добавление слоя символов в Azure Maps | Документация Майкрософт
description: Сведения о добавлении символов на карту Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3bce4922a33648f5d7c0d211dba126f35603239b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68849278"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Добавление слоя символов на карту

В этой статье показано, как точки данных из источника могут преобразоваться для просмотра в виде слоя символов на карте. Слои символов подготавливаются к просмотру с помощью WebGL и поддерживают гораздо большие наборы точек, чем маркеры HTML, но не поддерживают традиционные элементы CSS и HTML для стилей.  

> [!TIP]
> Слои символов по умолчанию отображают координаты всех геометрических объектов в источнике данных. Чтобы ограничить слой таким образом, чтобы он отображал только возможности геометрических точек `filter` , задайте для `['==', ['geometry-type'], 'Point']` свойства слоя значение `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` или, если требуется включить компоненты MultiPoint.

## <a name="add-a-symbol-layer"></a>Добавление слоя символов

<iframe height='500' scrolling='no' title='Переключение расположения маркера' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Переключение расположения маркера</a> от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке кода, приведенном выше, создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). [Функция](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature) геоjson, содержащая геометрию [точки](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) , упаковывается классом [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) , чтобы упростить его обновление, а затем создать и добавить в источник данных.

В третьем блоке кода создается [прослушиватель событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) и обновляются координаты точки по щелчку мыши с помощью метода [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) класса Shape.

В [слое символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются для просмотра в качестве символов на карте с помощью текста или значков.  Источник данных, прослушиватель событий щелчка и слой символов создаются и добавляются к сопоставлению в `ready` функции прослушивателя [событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , чтобы убедиться, что точка отображается после загрузки и подготовки к доступу.

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

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [SymbolLayer class](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) (Класс SymbolLayer)

> [!div class="nextstepaction"]
> [SymbolLayerOptions interface](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) (Интерфейс SymbolLayerOptions)

> [!div class="nextstepaction"]
> [IconOptions interface](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest) (Интерфейс IconOptions)

> [!div class="nextstepaction"]
> [TextOptions interface](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest) (Интерфейс TextOptions)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Добавление всплывающего окна](map-add-popup.md)

> [!div class="nextstepaction"]
> [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Использование шаблонов изображений](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Добавление фигуры](map-add-shape.md)

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков на карту](map-add-bubble-layer.md)
