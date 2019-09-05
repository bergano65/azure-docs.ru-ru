---
title: Получение данных фигур из диспетчера рисунков в Azure Maps | Документация Майкрософт
description: Как получить данные фигуры с помощью веб-пакета SDK Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e7706fba1efad1bd0ce7110e129dcf113689af9a
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309842"
---
# <a name="get-shape-data"></a>Получение данных фигуры

В этой статье показано, как получить данные фигур, которые были отображены на карте, с помощью функции [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--) **Дравингманажер.-Source ()** . Возможны различные сценарии, в которых может потребоваться извлечь данные геообъектно-JSON рисуемой фигуры и использовать ее в других местах.  


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

<iframe height="686" title="Получение данных фигуры" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>См. раздел <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>Получение данных формы</a> с помощью Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() в <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);

> [!div class="nextstepaction"]
> [Диспетчер рисунков](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Панель инструментов рисования](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
