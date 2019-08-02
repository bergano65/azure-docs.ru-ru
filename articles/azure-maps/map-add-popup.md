---
title: Добавление всплывающего окна с помощью службы "Карты Azure" | Документация Майкрософт
description: Как добавить всплывающее окно на карту в JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 92d44ef3d0db8e93d4babd7441238c7fa105dbd5
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639000"
---
# <a name="add-a-popup-to-the-map"></a>Добавление всплывающего окна на карту

В этой статье объясняется, как добавить всплывающее окно в точку на карте.

## <a name="understand-the-code"></a>Изучение кода

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Добавление всплывающего окна с помощью Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Добавление всплывающего окна с помощью Azure Maps</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md). Будет также создано содержимое HTML, которое будет отображаться во всплывающем окне.

Во втором блоке кода создается объект источника данных с помощью класса [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Точка является [компонентом](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) класса [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest). Затем создается объект точки со свойствами имени и описания, который добавляется в источник данных.

В [слое символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются для просмотра в качестве символов на карте с помощью текста или значков.  Слой символов создается в третьем блоке кода. Источник данных добавляется в слой символов, который затем добавляется на карту.

В четвертом блоке кода создается [объект всплывающего окна](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) с помощью `new atlas.Popup()`. Такие свойства всплывающего окна, как position и pixelOffset, входят в класс [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions). PopupOptions можно определить в конструкторе выплывающего окна или с помощью функции [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) класса Popup. Затем создается прослушиватель событий `mouseover` для слоя символов.

Последний блок кода создает функцию, которая инициируется прослушивателем событий `mouseover`. Он задает содержимое и свойства всплывающего окна и добавляет объект всплывающего окна на карту.

## <a name="reusing-a-popup-with-multiple-points"></a>Повторное использование всплывающего окна с несколькими точками

Если у вас есть большое количество точек и вы хотите отображать только одно всплывающее окно за раз, лучше всего создать одно всплывающее окно и повторно использовать его вместо того, чтобы создавать всплывающее окно для каждой функции точки. Таким образом, число элементов DOM, созданных приложением, значительно снизится, обеспечивая более высокую производительность. Этот пример создает три функции точки. Если щелкнуть любую из них, отобразится всплывающее окно с содержимым этой функции точки.

<br/>

<iframe height='500' scrolling='no' title='Повторное использование всплывающего окна с несколькими закреплениями' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>повторного использования всплывающего окна с несколькими закреплениями</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
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
> [Добавление фигуры](./map-add-shape.md)