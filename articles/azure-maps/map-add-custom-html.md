---
title: Добавление маркера HTML в Azure Maps | Документация Майкрософт
description: Как добавить на карту Javascript маркер HTML
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 1c812a77429e13ea39b2f4946043c13e10aaf097
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769674"
---
# <a name="add-html-markers-to-the-map"></a>Добавление маркеров HTML на карту

В этой статье показано, как добавить на карту настраиваемый элемент HTML, например файл изображения, в качестве маркера HTML.

> [!NOTE]
> Маркеры HTML не подключаются к источникам данных. Вместо этого сведения о положении добавляются непосредственно к маркеру, который добавляется в свойство карт `markers` — [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> В отличие от большинства слоев в веб-элементе управления Azure Maps, использующих WebGL для отрисовки, маркеры HTML используют традиционные элементы модели DOM для отрисовки. Таким образом, чем больше дополнительных маркеров HTML добавлено на страницу, тем больше элементов модели DOM. Производительность может снизиться после добавления нескольких сотен маркеров HTML. Для более крупных наборов данных рекомендуется кластеризация данных или использование слоя символа или пузырьков.

## <a name="add-an-html-marker"></a>Добавление маркера HTML

Класс HtmlMarker имеет стиль по умолчанию. Маркер можно настроить, задав параметры цвета и текста маркера. Стиль по умолчанию класса HtmlMarker — это шаблон SVG с заполнителем цвета и текста. Задайте свойства цвета и текста в параметрах HtmlMarker для быстрой настройки. 

<br/>

<iframe height='500' scrolling='no' title='Добавление на карту маркера HTML' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Добавление маркера HTML на карту</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода на карту добавляется [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) с использованием свойства [markers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) класса [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). HtmlMarker добавляется на карту с помощью функции [прослушивателя событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Это обеспечивает отображение только после полной загрузки карты.

## <a name="create-svg-templated-html-marker"></a>Создание маркера HTML шаблона SVG

Значение по умолчанию `htmlContent` маркера HTML — это шаблон SVG с папками места `{color}` и `{text}`. Вы можете создавать настраиваемые строки SVG и добавлять эти примеры заполнителей в свои SVG, чтобы при настройке параметров `color` и `text` маркера заполнители обновлялись в SVG.

<br/>

<iframe height='500' scrolling='no' title='Маркер HTML с пользовательским шаблоном SVG' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>Маркер HTML с пользовательским шаблоном SVG</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-css-styled-html-marker"></a>Добавление маркера HTML стиля CSS

Одно из преимуществ HTML заключается в том, что имеется множество отличных настроек, которые можно сделать с помощью CSS. В этом примере содержимое HtmlMarker состоит из HTML и CSS, которые создают анимированное закрепление, фиксируемое на месте и действующее динамически.

<br/>

<iframe height='500' scrolling='no' title='Источник данных HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Посмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>Источник данных HTML</a> <a href='https://codepen.io/azuremaps'>от Azure Maps (@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Перетаскиваемые маркеры HTML

В этом примере показано, как сделать перетаскиваемый маркер HTML. Маркеры HTML поддерживают события `drag`, `dragstart` и `dragend`.

<br/>

<iframe height='500' scrolling='no' title='Перетаскиваемый маркер HTML' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>Перетаскиваемый маркер HTML</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Добавление событий мыши к маркерам HTML

Эти примеры демонстрируют добавление событий мыши и перетаскивания к маркеру HTML.

<br/>

<iframe height='500' scrolling='no' title='Добавление событий мыши к маркерам HTML' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>Добавление событий мыши к маркерам HTML</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Добавление слоя символов](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Добавление слоя пузырьков](./map-add-bubble-layer.md)