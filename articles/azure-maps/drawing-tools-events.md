---
title: Добавление панели инструментов рисования в карту | Карты Microsoft Azure
description: В этой статье вы узнаете, как добавить панель инструментов рисования на карту с помощью веб-пакета SDK Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: cf9c79f608aa3ffd1137be41ff3348f62b890867
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198315"
---
# <a name="drawing-tool-events"></a>События инструмента рисования

При использовании средств рисования на карте полезно реагировать на определенные события, когда пользователь рисует на карте. В этой таблице перечислены все события, поддерживаемые классом `DrawingManager`.

| Событие | Описание |
|-------|-------------|
| `drawingchanged` | Срабатывает при добавлении или изменении любой координаты в фигуре. | 
| `drawingchanging` | Активируется при отображении любой координаты предварительного просмотра для фигуры. Например, это событие будет срабатывать несколько раз при перетаскивании координат. | 
| `drawingcomplete` | Возникает после завершения рисования фигуры или выхода из режима редактирования. |
| `drawingmodechanged` | Возникает при изменении режима рисования. Новый режим рисования передается в обработчик событий. |
| `drawingstarted` | Возникает, когда пользователь начинает рисовать фигуру или помещает фигуру в режим правки.  |

В следующем коде показано, как работают события в модуле средств рисования. Нарисуйте фигуры на карте и просмотрите их при срабатывании событий.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="События инструментов рисования" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>события инструментов рисования</a> пера по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Примеры

Рассмотрим некоторые распространенные сценарии, в которых используются события средств рисования.

### <a name="select-points-in-polygon-area"></a>Выбор точек в области многоугольника

Этот код демонстрирует, как отслеживать событие пользовательских фигур. В этом примере код отслеживает фигуры многоугольников, прямоугольников и кругов. Затем он определяет, какие точки данных на карте находятся в рисуемой области. Событие `drawingcomplete` используется для активации логики выбора. В логике выбора код выполняет перебор всех точек данных на карте. Он проверяет, существует ли пересечение точки и области рисуемой фигуры. В этом примере используется библиотека [Турф. js](https://turfjs.org/) с открытым кодом для выполнения вычисления пространственного пересечения.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Выбор данных в области рисования многоугольника" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Посмотрите на перо, <a href='https://codepen.io/azuremaps/pen/XWJdeja'>выберите данные в рисуемой области многоугольника</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Рисование и поиск в области многоугольника

Этот код выполняет поиск точек интересов в области фигуры после того, как пользователь завершил Рисование фигуры. Чтобы изменить и выполнить код, нажмите кнопку "изменить в коде пера" в правом верхнем углу рамки. Событие `drawingcomplete` используется для активации логики поиска. Если пользователь рисует прямоугольник или многоугольник, выполняется поиск в геометрии. При рисовании окружности точка радиуса и Центральная используется для выполнения поиска в процентах. Событие `drawingmodechanged` используется, чтобы определить, когда пользователь переключается в режим рисования, и это событие очищает полотно.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Рисование и поиск в области многоугольника" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>Рисование и поиск в области многоугольников</a> с помощью Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Создание инструмента измерения

В приведенном ниже коде показано, как можно использовать события рисования для создания инструмента измерения. `drawingchanging` используется для отслеживания формы, как она рисуется. По мере того, как пользователь перемещает мышь, выполняется вычисление размеров фигуры. Событие `drawingcomplete` используется для выполнения окончательного вычисления фигуры после ее прорисовки. Событие `drawingmodechanged` используется, чтобы определить, когда пользователь переключается в режим рисования. Кроме того, событие `drawingmodechanged` очищает холст рисования и очищает старую информацию об измерении.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Измерительный инструмент" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>инструмент измерения</a> пера, Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Следующие шаги

Узнайте, как использовать дополнительные функции модуля "инструменты рисования":

> [!div class="nextstepaction"]
> [Получение данных фигуры](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Типы взаимодействия и сочетания клавиш](drawing-tools-interactions-keyboard-shortcuts.md)

Дополнительные сведения о модуле "службы":

> [!div class="nextstepaction"]
> [Модуль служб](how-to-use-services-module.md)

Изучите дополнительные примеры кода:

> [!div class="nextstepaction"]
> [Страница с примерами кода](https://aka.ms/AzureMapsSamples)
