---
title: Добавление панели инструментов рисования в карту | Карты Microsoft Azure
description: Добавление панели инструментов рисования в карту с помощью веб-пакета SDK Azure Maps
author: farah-alyasari
ms.author: v-faalya
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cb0f70bc42c9ac0f7026c910593950516f027a88
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209755"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Добавление панели инструментов средств рисования на карту

В этой статье показано, как использовать модуль средств рисования и отображать панель инструментов рисования на карте. Элемент управления [дравингтулбар](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) добавляет панель инструментов рисования на карте. Вы узнаете, как создавать карты с одним и всеми инструментами рисования, а также как настраивать отрисовку графических фигур в диспетчере рисунков.

## <a name="add-drawing-toolbar"></a>Добавление панели инструментов рисования

Следующий код создает экземпляр диспетчера рисунков и отображает панель инструментов на карте.

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Ниже приведен полный пример выполнения кода функции, приведенной выше.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Добавление панели инструментов рисования" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>Добавление панели инструментов рисования</a> с помощью Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Параметр "ограничить отображаемые параметры панели инструментов"

Следующий код создает экземпляр диспетчера рисунков и отображает панель инструментов, используя только инструмент рисования многоугольников на карте. 

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Ниже приведен полный пример выполнения кода функции, приведенной выше.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Добавление инструмента рисования многоугольников" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Ознакомьтесь с пером <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>Добавление инструмента рисования многоугольника</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Изменить стиль отрисовки изображения

Следующий код получает слои подготовки отчетов из диспетчера рисунков и изменяет их параметры, чтобы изменить стиль отрисовки для рисования. В этом случае точки отображаются с синим значком маркера. Линии будут иметь красный и четыре пиксела в ширину. Многоугольники будут иметь зеленый цвет заливки и оранжевый контур.

```Javascript
var layers = drawingManager.getLayers();
    layers.pointLayer.setOptions({
        iconOptions: {
            image: 'marker-blue'
        }
    });
    layers.lineLayer.setOptions({
        strokeColor: 'red',
        strokeWidth: 4
    });
    layers.polygonLayer.setOptions({
        fillColor: 'green'
    });
    layers.polygonOutlineLayer.setOptions({
        strokeColor: 'orange'
    });
```

Ниже приведен полный пример выполнения кода функции, приведенной выше.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Изменить стиль отрисовки изображения" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>изменение перьевого отображения изображения</a> с Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Следующие шаги

Узнайте, как использовать дополнительные функции модуля "инструменты рисования":

> [!div class="nextstepaction"]
> [Получение данных фигуры](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Реакция на события прорисовки](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Типы взаимодействия и сочетания клавиш](drawing-tools-interactions-keyboard-shortcuts.md)

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Схема](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Панель инструментов рисования](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Диспетчер рисунков](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)
