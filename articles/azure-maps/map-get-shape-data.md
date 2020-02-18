---
title: Получение данных из фигур на карте | Карты Microsoft Azure
description: Из этой статьи вы узнаете, как получить данные фигуры на карте с помощью веб-пакета SDK Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 63d947b85e75e3809445c5bc65577aeaed38caa1
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209670"
---
# <a name="get-shape-data"></a>Получение данных о фигуре

В этой статье показано, как получить данные фигур, рисуемых на карте. Мы используем функцию **дравингманажер.-Source ()** в [диспетчере рисунков](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--). Существуют различные сценарии, когда необходимо извлечь данные геообъектно-JSON рисуемой фигуры и использовать их в других местах.  


## <a name="get-data-from-drawn-shape"></a>Получение данных из рисуемой фигуры

Следующая функция получает исходные данные рисуемой фигуры и выводит их на экран. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Ниже приведен полный пример выполнения кода, в котором можно нарисовать фигуру для проверки функциональности:

<br/>

<iframe height="686" title="Получение данных о фигуре" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>См. раздел <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>Получение данных фигуры</a> с помощью Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Следующие шаги

Узнайте, как использовать дополнительные функции модуля "инструменты рисования":

> [!div class="nextstepaction"]
> [Реакция на события прорисовки](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Типы взаимодействия и сочетания клавиш](drawing-tools-interactions-keyboard-shortcuts.md)

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Схема](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Диспетчер рисунков](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Панель инструментов рисования](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
