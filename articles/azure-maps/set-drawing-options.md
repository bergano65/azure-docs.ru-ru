---
title: Установка параметров рисования в Azure Maps | Документация Майкрософт
description: Установка данных параметров рисования с помощью веб-пакета SDK Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d85525274db7818737b62ad4e9ea2026b8aef3b2
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309762"
---
# <a name="set-drawing-options"></a>Настройка параметров рисования

В этой статье показано, как различные параметры [диспетчера рисунков](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) изменяют взаимодействие с пользователем. Можно указать параметры для диспетчера рисунков при создании экземпляра или использовать функцию **дравингманажер. сетоптионс ()** для задания параметров.


## <a name="set-drawing-mode"></a>Установка режима рисования

Следующий код создает экземпляр диспетчера рисунков и устанавливает параметр **режима** рисования. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Приведенный ниже код является полным запуском примера того, как настроить режим рисования диспетчера рисунков. Щелкните карту, чтобы начать рисование многоугольника.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Рисование многоугольника" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. перо <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>Рисование многоугольника</a> на Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="set-interaction-type"></a>Задать тип взаимодействия

Следующий код задает тип взаимодействия рисования, которым должен соответствовать Диспетчер рисунков. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

Ниже приведен пример кода, реализующий функциональность, которая позволяет свободно рисовать на карте, удерживая нажатой левую кнопку мыши и перетащив ее. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Произвольная прорисовка" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>рисунок с изображением без</a> пера, Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="customizing-drawing-options"></a>Настройка параметров рисования

В предыдущих примерах было показано, как настроить параметры рисования при создании экземпляра диспетчера рисунков. Параметры диспетчера рисунков также можно задать с помощью функции **дравингманажер. сетоптионс ()** . Ниже приведен инструмент для проверки настройки всех параметров диспетчера рисунков с помощью функции Сетоптионс.

<br/>

<iframe height="685" title="Настройка диспетчера рисунков" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>См. раздел <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>Получение данных формы</a> с помощью Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() в <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);

> [!div class="nextstepaction"]
> [Диспетчер рисунков](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Панель инструментов рисования](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
