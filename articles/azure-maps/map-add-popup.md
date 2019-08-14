---
title: Добавление всплывающего окна с помощью службы "Карты Azure" | Документация Майкрософт
description: Добавление всплывающего окна в веб-пакет SDK Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cde6c745034d0963bd372e36e6e5a046113c202b
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976551"
---
# <a name="add-a-popup-to-the-map"></a>Добавление всплывающего окна на карту

В этой статье объясняется, как добавить всплывающее окно в точку на карте.

## <a name="understand-the-code"></a>Изучение кода

Следующий код добавляет функцию Point, которая имеет `name` свойства и `description` , в карту с помощью слоя символов. Экземпляр [класса Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) создан, но не отображается. События мыши добавляются на слой символов для активации открытия и закрытия всплывающего окна при наведении указателя мыши на маркер символа. При наведении указателя мыши `position` свойство всплывающего окна обновляется с указанием позиции маркера, `content` а параметр обновляется с помощью HTML-кода, который заключает в `name` оболочку свойства `description` и функции точки, на которую наведен указатель мыши. Затем всплывающее окно отображается на карте с помощью его `open` функции.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Ниже приведен полный пример выполнения кода описанной выше функциональности.

<br/>

<iframe height='500' scrolling='no' title='Добавление всплывающего окна с помощью Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Добавление всплывающего окна с помощью Azure Maps</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Повторное использование всплывающего окна с несколькими точками

При наличии большого количества точек и последующего отображения только одного всплывающего окна лучшим подходом является создание одного всплывающего окна и его повторное использование вместо создания всплывающего окна для каждой функции точки. При повторном использовании всплывающего окна количество элементов DOM, созданных приложением, значительно сокращается, что может повысить производительность. В следующем примере создаются 3-точечные функции. Если щелкнуть любую из них, отобразится всплывающее окно с содержимым этой функции точки.

<br/>

<iframe height='500' scrolling='no' title='Повторное использование всплывающего окна с несколькими закреплениями' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>повторного использования всплывающего окна с несколькими закреплениями</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Настройка всплывающего окна

По умолчанию всплывающее окно имеет белый фон, стрелку указателя внизу и кнопку Закрыть в правом верхнем углу. В следующем примере цвет фона изменяется на черный с помощью `fillColor` параметра всплывающего окна. Кнопка Закрыть удаляется путем присвоения `shoCloseButton` параметру значения false. В HTML-содержимом всплывающего окна используется заполнение 10 пикселей от краев всплывающего окна, а текст становится белым, поэтому он хорошо отображается на черном фоне.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Настраиваемое всплывающее окно" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>всплывающее окно</a> , настроенное<a href='https://codepen.io/azuremaps'>@azuremaps</a>пером, Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>События всплывающего окна

Всплывающие окна можно открывать, закрывать и перетаскивать. Всплывающий класс предоставляет события, которые помогают разработчикам реагировать на эти действия. В следующем примере показаны события, которые запускаются при открытии, закрытии или перетаскивании всплывающего окна. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="События всплывающего окна" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>события всплывающего окна</a> пера по<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

Полные примеры кода см. в следующих превосходных статьях:

> [!div class="nextstepaction"]
> [Добавление слоя символов](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Добавление маркера HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Добавить слой линий](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Добавление слоя многоугольников](map-add-shape.md)