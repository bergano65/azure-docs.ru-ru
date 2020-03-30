---
title: Добавление элементов управления на карту Карты Microsoft Azure
description: Как добавить элемент управления масштабирования, управление шагом, повернуть управление и сборщик стилей на карту в Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 094dc9fd01ec71f378a173a2b4fa64cc672d7c97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334558"
---
# <a name="add-controls-to-a-map"></a>Добавление элементов управления на карту

В этой статье показано, как добавить элементы управления на карту. Вы также узнаете, как создать карту со всеми элементами управления и [сборщиком стилей.](https://docs.microsoft.com/azure/azure-maps/choose-map-style)

## <a name="add-zoom-control"></a>Добавление элемента управления масштабом

Управление масштабированием добавляет кнопки для увеличения и изготавливание карты. Следующий пример кода создает экземпляр класса [ЗумКонтроль](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) и добавляет его в правый нижний угол карты.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Ниже приводится полный выборка кода приведена вышеуказанной функциональности.

<br/>

<iframe height='500' scrolling='no' title='Добавление элемента управления масштабом' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Добавление элемента управления масштабом</a> для службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Добавление элемента управления наклоном

Управление шагом добавляет кнопки для наклона поля для отображения относительно горизонта. Следующий пример кода создает экземпляр класса [PitchControl.](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) Он добавляет PitchControl в верхний правый угол карты.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Ниже приводится полный выборка кода приведена вышеуказанной функциональности.

<br/>

<iframe height='500' scrolling='no' title='Добавление элемента управления наклоном' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Добавление элемента управления наклоном</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Добавление элемента управления расстоянием

Управление компасом добавляет кнопку для вращения карты. Следующий пример кода создает экземпляр класса [Compass Control](/javascript/api/azure-maps-control/atlas.control.compasscontrol) и добавляет его в левый нижний угол карты.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Ниже приводится полный выборка кода приведена вышеуказанной функциональности.

<br/>

<iframe height='500' scrolling='no' title='Добавление элемента управления поворотом' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Добавление элемента управления поворотом</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>Карта со всеми элементами управления

Несколько элементов управления могут быть помещены в массив и добавлены на карту все сразу и расположены в той же области карты для упрощения разработки. Следующие добавлены стандартные элементы управления навигацией на карту, используя этот подход.

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

Следующий пример кода добавляет зум, компас, шаг и элементы управления сборщиком стилей в верхний правый угол карты. Обратите внимание, как они автоматически складываются. Порядок выполнения элементов управления из сценария определяет их порядок появления на карте. Чтобы изменить порядок элементов управления на карте, можно изменить их порядок в массиве.

<br/>

<iframe height='500' scrolling='no' title='Карта со всеми элементами управления' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>Карта со всеми элементами управления </a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

Управление сборщиком стилей определяется классом [StyleControl.](/javascript/api/azure-maps-control/atlas.control.stylecontrol) Для получения дополнительной информации об использовании управления сборщиком стилей [см.](choose-map-style.md)

## <a name="customize-controls"></a>Настройка элементов управления

Вот инструмент для проверки различных вариантов настройки элементов управления.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Параметры управления навигацией" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Ознакомьтесь <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>с вариантами управления навигацией</a> pen по картам Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>на <a href='https://codepen.io'>CodePen</a>.
</iframe>

Если вы хотите создать индивидуальные элементы навигации, `atlas.Control` создайте класс, который простирается от класса или создайте html элемент и расположите его над картой div. Поиметь этот элемент управления `setCamera` uI вызова карты функции для перемещения карты. 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Компас управления](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol); 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol). 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol); 

Полные примеры кода см. в следующих статьях:

> [!div class="nextstepaction"]
> [Добавление ПИН-кода](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Добавление всплывающего окна](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Добавление слоя линий](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя многоугольников](map-add-shape.md)

> [!div class="nextstepaction"]
> [Добавить слой пузыря](map-add-bubble-layer.md)

