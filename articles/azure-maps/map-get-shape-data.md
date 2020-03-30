---
title: Получайте данные из форм на карте Карты Microsoft Azure
description: В этой статье узнайте, как получить данные формы, нарисованные на карте, с помощью веб-SDK Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 88db018575f92e777223f383c65cd6db51ba515a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334408"
---
# <a name="get-shape-data"></a>Получение данных о фигуре

В этой статье показано, как получить данные о формах, которые нарисованы на карте. Мы используем функцию **drawingManager.getSource()** внутри [менеджера рисования.](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--) Существуют различные сценарии, когда требуется извлечь данные геойсона нарисованной формы и использовать их в другом месте.  


## <a name="get-data-from-drawn-shape"></a>Получайте данные из нарисованной формы

Следующая функция получает исходные данные нарисованной формы и выводит их на экран. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Ниже приведен полный пример кода, где вы можете нарисовать форму для проверки функциональности:

<br/>

<iframe height="686" title="Получение данных о фигуре" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Смотрите <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>данные</a> о форме Pen<a href='https://codepen.io/azuremaps'>@azuremaps</a>Get по Картам Azure () на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как использовать дополнительные функции модуля инструментов рисования:

> [!div class="nextstepaction"]
> [Реакция на события рисования](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Типы взаимодействия и сочетания клавиш](drawing-tools-interactions-keyboard-shortcuts.md)

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Карта](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Менеджер по рисованию](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Панель инструментов рисования](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
