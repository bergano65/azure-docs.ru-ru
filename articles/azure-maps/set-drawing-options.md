---
title: Модуль инструментов рисования Карты Microsoft Azure
description: В этой статье вы узнаете, как настроить данные вариантов рисования с помощью веб-SDK Microsoft Azure Maps Web SDK
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 711609f9382e2153cbc738d544933796dbbe2e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334313"
---
# <a name="use-the-drawing-tools-module"></a>Использование модуля средств рисования

Azure Maps Web SDK предоставляет *модуль инструментов рисования.* Этот модуль позволяет легко рисовать и отстранять фигуры на карте с помощью вхотливого устройства, такого как мышь или сенсорный экран. Основным классом этого модуля является [менеджер рисования.](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) Менеджер чертежа предоставляет все возможности, необходимые для рисования и отображения фигур на карте. Он может быть использован непосредственно, и он интегрирован с пользовательским пользовательским пользовательским пользовательским системой инструментов. Вы также можете использовать встроенный класс [панели инструментов для рисования.](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Загрузка модуля инструментов рисования на веб-странице

1. Создайте новый HTML-файл и [реализуйте карту в обычном режиме.](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)
2. Загрузите модуль инструментов для рисования Azure Maps. Вы можете загрузить его одним из двух способов:
    - Используйте глобально размещенную версию системы доставки содержимого Azure ВИО в модуле сервисов Azure Maps. Добавьте ссылку на таблицу JavaScript `<head>` и CSS в элемент файла:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - Кроме того, можно загрузить модуль инструментов рисования для исходного кода Azure Maps Web SDK локально, используя пакет [npm лазурных карт-чертежных инструментов,](https://www.npmjs.com/package/azure-maps-drawing-tools) а затем разместить его с вашим приложением. Этот пакет также включает определения TypeScript. Выполните эту команду:
    
        > **npm установить лазурные карты-рисунок-инструменты**
    
        Затем добавьте ссылку на таблицу JavaScript `<head>` и CSS в элемент файла:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>Используйте менеджер рисования напрямую

После загрузки модуля инструментов рисования в приложении можно включить возможности рисования и редактирования с помощью [менеджера рисования.](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) Можно указать параметры для менеджера рисования, мгновенно его `drawingManager.setOptions()` или в качестве альтернативы использовать функцию.

### <a name="set-the-drawing-mode"></a>Установите режим рисования

Следующий код создает экземпляр менеджера чертежа и устанавливает опцию **режима** рисования. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Приведенный ниже код представляет собой полный пример того, как установить режим рисования менеджера чертежа. Нажмите на карту, чтобы начать рисовать полигон.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Нарисуйте полигон" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите Pen <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>Draw полигон</a> по Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps ( ) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Установка типа взаимодействия

Менеджер чертежа поддерживает три различных способа взаимодействия с картой для рисования фигур.

* `click`- Координаты добавляются при нажатии мыши или касания.
* `freehand `- Координаты добавляются, когда мышь или прикосновение перетаскивается на карте. 
* `hybrid`- Координаты добавляются при нажатии мыши или касания или перетаскиваемых.

Следующий код позволяет режимрисовать полигона и устанавливает тип взаимодействия чертежа, которого должен придерживаться `freehand`менеджер чертежа. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 Этот пример кода реализует функциональность рисования полигона на карте. Просто удерживайте левую кнопку мыши и перетащите ее вокруг, свободно.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Рисунок свободной руки" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите рисунок Pen <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>Free-hand</a> от<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="customizing-drawing-options"></a>Настройка вариантов рисования

Предыдущие примеры показали, как настроить параметры рисования при мгновенном настройке менеджера рисования. Вы также можете настроить параметры менеджера `drawingManager.setOptions()` рисования с помощью функции. Ниже приведен инструмент для проверки настройки всех параметров для менеджера рисования с помощью функции setOptions.

<br/>

<iframe height="685" title="Настройка менеджера рисования" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Смотрите <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>данные</a> о форме Pen<a href='https://codepen.io/azuremaps'>@azuremaps</a>Get по Картам Azure () на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как использовать дополнительные функции модуля инструментов рисования:

> [!div class="nextstepaction"]
> [Добавление панели средств рисования](map-add-drawing-toolbar.md)

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
> [Менеджер по рисованию](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Панель инструментов рисования](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
