---
title: Добавьте панель инструментов для рисования на карту Карты Microsoft Azure
description: Как добавить панель инструментов для рисования на карту с помощью Azure Maps Web SDK
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: bebf1ddfbca3aec5a551193609381cf3510bc3ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334499"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Добавление панели инструментов рисования на карту

В этой статье показано, как использовать модуль Drawing Tools и отображать панель инструментов рисования на карте. Элемент управления [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) добавляет панель инструментов для рисования на карте. Вы узнаете, как создавать карты только с одним и всеми инструментами рисования и как настроить визуализацию фигур чертежа в менеджере чертежа.

## <a name="add-drawing-toolbar"></a>Добавление панели инструментов рисования

Следующий код создает экземпляр менеджера чертежа и отображает панель инструментов на карте.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Ниже приводится полный приведен образец кода работает над ним:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Добавление панели инструментов рисования" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>панель инструментов</a> Pen Add drawing<a href='https://codepen.io/azuremaps'>@azuremaps</a>на Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Ограничьте отображаемые параметры панели инструментов

Следующий код создает экземпляр менеджера чертежа и отображает панель инструментов с помощью инструмента для рисования полигона на карте. 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Ниже приводится полный приведен образец кода работает над ним:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Добавить инструмент для рисования полигона" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите Pen <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>Добавить полигон рисунок инструмент</a> Azure Maps ( )<a href='https://codepen.io/azuremaps'>@azuremaps</a>на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Изменение стиля визуализации рисунка

Стиль нарисованных фигур можно настроить путем извлечения основных слоев менеджера рисования с помощью `drawingManager.getLayers()` функции, а затем настройки параметров на отдельных слоях. Ручки перетаскивания, которые появляются для координат при редактировании формы, являются HTML маркерами. Стиль перетаскивания можно настроить, передавая параметры HTML-маркера `dragHandleStyle` в `secondaryDragHandleStyle` и варианты менеджера рисования.  

Следующий код получает слои рендеринга от менеджера рисования и изменяет их параметры, чтобы изменить стиль рендеринга для рисования. В этом случае точки будут отображаться с синим значком маркера. Линии будут красными и четыре пикселя в ширину. Полигоны будут иметь зеленый цвет заполнения и оранжевый контур. Затем он изменяет стили перетаскивания, чтобы быть квадратными иконками. 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

Ниже приводится полный приведен образец кода работает над ним:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Изменение стиля визуализации рисунка" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>стиль визуализации рисунка Pen</a> Change<a href='https://codepen.io/azuremaps'>@azuremaps</a>по средством Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как использовать дополнительные функции модуля инструментов рисования:

> [!div class="nextstepaction"]
> [Получение данных о фигуре](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Реакция на события рисования](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Типы взаимодействия и сочетания клавиш](drawing-tools-interactions-keyboard-shortcuts.md)

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Карта](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Панель инструментов рисования](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Менеджер по рисованию](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)
