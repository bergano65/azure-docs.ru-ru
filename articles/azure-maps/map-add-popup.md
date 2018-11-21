---
title: Добавление всплывающего окна с помощью службы "Карты Azure" | Документация Майкрософт
description: Как добавить всплывающее окно на карту в JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0be10c155398133887fadb1fe9954068f3afb9d9
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568120"
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

В четвертом блоке кода создается [объект всплывающего окна](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) с помощью `new atlas.Popup()`. Такие свойства всплывающего окна, как position и pixelOffset, входят в класс [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest). PopupOptions можно определить в конструкторе выплывающего окна или с помощью функции [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) класса Popup. Затем создается прослушиватель событий `mouseover` для слоя символов.

Последний блок кода создает функцию, которая инициируется прослушивателем событий `mouseover`. Он задает содержимое и свойства всплывающего окна и добавляет объект всплывающего окна на карту.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Полные примеры кода см. в следующих превосходных статьях:

> [!div class="nextstepaction"]
> [Добавление фигуры](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Добавление пользовательского HTML-кода](./map-add-custom-html.md)
