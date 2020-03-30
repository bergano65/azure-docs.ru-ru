---
title: Добавьте панель инструментов для рисования на карту Карты Microsoft Azure
description: В этой статье вы узнаете, как добавить панель инструментов для рисования на карту с помощью Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: cf9c79f608aa3ffd1137be41ff3348f62b890867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198315"
---
# <a name="drawing-tool-events"></a>Рисование событий инструмента

При использовании инструментов рисования на карте полезно реагировать на определенные события по мере того, как пользователь рисует на карте. В этой таблице перечислены все `DrawingManager` события, поддерживаемые классом.

| Событие | Описание |
|-------|-------------|
| `drawingchanged` | Выстрел, когда любая координат в форме была добавлена или изменена. | 
| `drawingchanging` | Выстрел при отображении координат предварительного просмотра для формы. Например, это событие будет стрелять несколько раз, как координаты перетаскивается. | 
| `drawingcomplete` | Выстрел, когда форма закончила быть нарисованы или выведены из режима изменения. |
| `drawingmodechanged` | Работается при изменении режима рисования. Новый режим рисования передается в обработчик событий. |
| `drawingstarted` | Увольняется, когда пользователь начинает рисовать фигуру или вводит форму в режим edit.  |

Следующий код показывает, как работают события в модуле Drawing Tools. Нарисуйте фигуры на карте и смотреть, как события огня.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="События, нарисовав инструменты," src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>события инструментов Pen Drawing</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>по картам Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Примеры

Давайте посмотрим некоторые общие сценарии, в которых используются события инструментов рисования.

### <a name="select-points-in-polygon-area"></a>Выберите точки в области полигона

В этом коде показано, как контролировать событие фигурирования пользовательского рисунка. Например, код отслеживает формы полигонов, прямоугольников и кругов. Затем определяется, какие точки данных на карте находятся в пределах нарисованной области. Событие `drawingcomplete` используется для запуска логики выбора. В выбранной логике код просматривает все точки данных на карте. Он проверяет, есть ли пересечение точки и области нарисованной формы. В этом примере используется библиотека [Turf.js](https://turfjs.org/) с открытым исходным кодом для выполнения расчета пространственного пересечения.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Выберите данные в нарисованной области полигона" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите данные Pen <a href='https://codepen.io/azuremaps/pen/XWJdeja'>Select в нарисованной области полигона</a> по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Рисовать и искать в области полигона

Этот код ищет точки интересов внутри области формы после того, как пользователь закончил рисовать фигуру. Вы можете изменить и выполнить код, нажав кнопку "Изменить на кодовой ручке" в правом верхнем углу кадра. Событие `drawingcomplete` используется для запуска логики поиска. Если пользователь рисует прямоугольник или полигон, выполняется поиск внутри геометрии. Если круг нарисован, радиус и положение центра используются для выполнения поиска точки интереса. Событие `drawingmodechanged` используется для определения того, когда пользователь переключается в режим чертежа, и это событие очищает холст чертежа.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Рисовать и искать в области полигона" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>Pen Draw и поиск в области полигона</a> по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Создание измерительного инструмента

В приведенном ниже коде показано, как события чертежа могут быть использованы для создания измерительного инструмента. Используется `drawingchanging` для мониторинга формы, как это обращается. При движении мыши размеры формы вычисляются. Событие `drawingcomplete` используется для окончательного расчета формы после того, как оно было нарисовано. Событие `drawingmodechanged` используется для определения того, когда пользователь переходит в режим чертежа. Кроме того, `drawingmodechanged` событие очищает холст чертежа и очищает старую информацию измерения.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Измерительный инструмент" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>инструмент измерения</a> пера по<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как использовать дополнительные функции модуля инструментов рисования:

> [!div class="nextstepaction"]
> [Получение данных о фигуре](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Типы взаимодействия и сочетания клавиш](drawing-tools-interactions-keyboard-shortcuts.md)

Подробнее о модуле Услуг:

> [!div class="nextstepaction"]
> [Модуль служб](how-to-use-services-module.md)

Изучите дополнительные примеры кода:

> [!div class="nextstepaction"]
> [Страница с примерами кода](https://aka.ms/AzureMapsSamples)
