---
title: Добавление панели инструментов рисования в Azure Maps | Документация Майкрософт
description: Добавление панели инструментов рисования в карту с помощью веб-пакета SDK Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 63848ecf92daecf28f6e0046e87da15355aa35c1
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561836"
---
# <a name="drawing-tool-events"></a>События инструмента рисования

При использовании средств рисования на карте часто бывает полезно реагировать на определенные события, когда пользователь рисует на карте. В следующей таблице перечислены все события, поддерживаемые классом `DrawingManager`.

| Мероприятие | Description |
|-------|-------------|
| `drawingchanged` | Срабатывает при добавлении или изменении любой координаты в фигуре. | 
| `drawingchanging` | Активируется при отображении любой координаты предварительного просмотра для фигуры. Например, будет срабатывать несколько раз при перетаскивании координат. | 
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

Ниже приведены примеры некоторых распространенных сценариев, в которых используются события средств рисования.

### <a name="select-points-in-polygon-area"></a>Выбор точек в области многоугольника

В следующем коде показано, как отслеживать Рисование фигур, представляющих области многоугольников (многоугольники, прямоугольники и круги), и определять, какие точки данных на карте находятся в области рисования. Событие `drawingcomplete` используется для активации логики выбора. В логике выбора все точки данных на карте проходят через циклический перебор и проверяются на пересечение с областью многоугольников рисуемой фигуры. В этом примере используется библиотека [Турф. js](https://turfjs.org/) с открытым кодом для выполнения вычисления пространственного пересечения.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Выбор данных в области рисования многоугольника" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Посмотрите на перо, <a href='https://codepen.io/azuremaps/pen/XWJdeja'>выберите данные в рисуемой области многоугольника</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Рисование и поиск в области многоугольника

В следующем коде показано, как выполнить поиск точек интересов в области фигуры после того, как пользователь завершит Рисование фигуры. Событие `drawingcomplete` используется для активации логики поиска. Если пользователь рисует прямоугольник или многоугольник, выполняется поиск в геометрии. При рисовании окружности точка радиуса и Центральная используется для выполнения поиска в процентах. Событие `drawingmodechanged` используется для определения времени переключения пользователя в режим рисования и очистки полотна.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Рисование и поиск в области многоугольника" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>Рисование и поиск в области многоугольников</a> с помощью Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Создание инструмента измерения

В следующем коде показано, как можно использовать события рисования для создания инструмента измерения. `drawingchanging` используется для отслеживания рисуемой фигуры. По мере того, как пользователь перемещает мышь, выполняется вычисление размеров фигуры. Событие `drawingcomplete` используется для выполнения окончательного вычисления фигуры после ее прорисовки. Событие `drawingmodechanged` используется, чтобы определить, когда пользователь переключается в режим рисования, и очищает полотно и старую информацию об измерениях.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Измерительный инструмент" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. раздел <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>инструмент измерения</a> пера, Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Дальнейшие действия

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
