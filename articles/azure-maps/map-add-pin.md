---
title: Добавление слоя символов в Azure Maps | Документация Майкрософт
description: Сведения о добавлении символов на карту Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 718a679418790a6bf1207a96e5c204f7962de239
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2019
ms.locfileid: "54411260"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Добавление слоя символов на карту

В этой статье показано, как точки данных из источника могут преобразоваться для просмотра в виде слоя символов на карте. Слои символов отображаются с помощью WebGL и поддерживают значительно больше точек данных, чем маркеры HTML, но не поддерживают традиционные элементы CSS и HTML для стилизации.  

> [!TIP]
> Слои символов по умолчанию отображают координаты всех геометрических объектов в источнике данных. Чтобы отображать в слое только геометрические объекты, присвойте параметру `filter` для этого слоя значение `['==', '$type', 'Point']`.

## <a name="add-a-symbol-layer"></a>Добавление слоя символов

<iframe height='500' scrolling='no' title='Переключение расположения маркера' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Переключение расположения маркера</a> от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке кода, приведенном выше, создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). [Функция], содержащая геометрию [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) (Точка), которая находится в оболочке класса [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) (Фигуры), чтобы его было проще обновлять, затем создавать и добавлять в источник данных.

В третьем блоке кода создается [прослушиватель событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) и обновляются координаты точки по щелчку мыши с помощью метода [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) класса Shape.

В [слое символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются для просмотра в качестве символов на карте с помощью текста или значков.  Источник данных, прослушиватель событий щелчка и слой символов создаются и добавляются на карту с помощью функции [прослушивателя событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Это обеспечивает отображение точек только после полной загрузки карты.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Добавление пользовательского значка в слой символов

Слои символов преобразовываются для просмотра с помощью WebGL. Таким образом, все ресурсы (например, образы значков) необходимо загрузить в контекст WebGL. В этом примере показано, как добавить пользовательский значок символа в ресурсы карты, а затем использовать его для отображения точечных данных с помощью пользовательского символа на карте. Свойство `textField` слоя символа требует указания выражения. В этом случае мы хотим отобразить свойство температуры точечного объекта в качестве текстового значения. Это можно сделать с помощью выражения `['get', 'temperature']`. 

<br/>

<iframe height='500' scrolling='no' title='Значок изображения пользовательских символов' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. ручку <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Custom Symbol Image Icon</a> (Значок изображения пользовательских символов) от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-symbol-layer"></a>Настройка слоя символа 

Слой символа имеет множество доступных вариантов стилизации. Вот средство для проверки этих различных параметров.

<br/>

<iframe height='700' scrolling='no' title='Параметры слоя символов' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите ручку <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Symbol Layer Options</a> (Параметры слоя символов) от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дополнительная информация

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
> [Добавление всплывающего окна](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Добавление фигуры](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков](./map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков на карту](./map-add-bubble-layer.md)
