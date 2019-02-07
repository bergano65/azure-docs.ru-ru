---
title: Добавление элементов управления картой в службе Azure Maps | Документация Майкрософт
description: Сведения о том, как добавить элементы управления масштабом, наклоном, поворотом и средство выбора стиля на карту в службе Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 4947977fbed2b4fe4bd47adcc4de6516090573ad
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55661315"
---
# <a name="add-map-controls-to-azure-maps"></a>Добавление элементов управления картой в службе Azure Maps

В этой статье объясняется, как добавить элементы управления на карту. Вы также узнаете, как создать карту со всеми элементами управления и [средством выбора стиля](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Добавление элемента управления масштабом

<iframe height='500' scrolling='no' title='Добавление элемента управления масштабом' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Добавление элемента управления масштабом</a> для службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке кода устанавливается ключ подписки и создается объект map (его стиль предварительно не задан). См. инструкции по [созданию карты](./map-create.md).

Элемент управления масштабом добавляет возможность уменьшать и увеличивать карту. С помощью атласа [ZoomControl](/javascript/api/azure-maps-control/atlas.controls.zoomcontrol) во втором блоке кода создается объект элемента управления масштабом, а затем с помощью метода [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) он добавляется на карту. Элемент управления масштабом находится в пределах карты **прослушивателя событий**, чтобы обеспечить его загрузку после полной загрузки карты.

## <a name="add-pitch-control"></a>Добавление элемента управления наклоном

<iframe height='500' scrolling='no' title='Добавление элемента управления наклоном' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Добавление элемента управления наклоном</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке кода устанавливается ключ подписки и создается объект map (его стиль предварительно не задан). См. инструкции по [созданию карты](./map-create.md).

Элемент управления наклоном добавляет возможность изменять наклон карты. С помощью атласа [PitchControl](/javascript/api/azure-maps-control/atlas.controls.pitchcontrol) во втором блоке кода создается объект элемента управления наклоном, а с помощью метода [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) он добавляется на карту. Элемент управления наклоном находится в пределах карты **прослушивателя событий**, чтобы обеспечить его загрузку после полной загрузки карты.

## <a name="add-compass-control"></a>Добавление элемента управления расстоянием

<iframe height='500' scrolling='no' title='Добавление элемента управления поворотом' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Добавление элемента управления поворотом</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке кода устанавливается ключ подписки и создается объект map (его стиль предварительно не задан). См. инструкции по [созданию карты](./map-create.md).

С помощью атласа [Compass Control](/javascript/api/azure-maps-control/atlas.controls.compasscontrol) во втором блоке кода создается объект элемента управления расстоянием. Он также создает объект элемента управления расстоянием на карте с помощью метода [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). Элемент управления расстоянием находится в пределах карты **прослушивателя событий**, чтобы обеспечить его загрузку после полной загрузки карты.

## <a name="a-map-with-all-controls"></a>Карта со всеми элементами управления

<iframe height='500' scrolling='no' title='Карта со всеми элементами управления' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>Карта со всеми элементами управления </a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке кода устанавливается ключ подписки и создается объект map (его стиль предварительно не задан). См. инструкции по [созданию карты](./map-create.md).

С помощью атласа [CompassControl](/javascript/api/azure-maps-control/atlas.controls.compasscontrol) во втором блоке кода создается объект элемента управления расстоянием, а с помощью метода [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) он добавляется на карту.

С помощью атласа [ZoomControl](/javascript/api/azure-maps-control/atlas.controls.zoomcontrol) в третьем блоке кода создается объект элемента управления масштабом, а с помощью метода [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) он добавляется на карту.

С помощью атласа [PitchControl](/javascript/api/azure-maps-control/atlas.controls.pitchcontrol) в четвертом блоке кода создает объект элемента управления наклоном, а с помощью метода [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) он добавляется на карту.

С помощью атласа [StyleControl](/javascript/api/azure-maps-control/atlas.controls.stylecontrol) в последнем блоке кода создается объект средства выбора стиля, а с помощью метода [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) он добавляется на карту. Все элементы управления добавляются в пределы карты **прослушивателя событий**. Это позволит гарантировать их загрузку после полной загрузки карты.

Порядок выполнения элементов управления из сценария определяет их порядок появления на карте. Чтобы изменить порядок элементов управления на карте, измените их порядок в сценарии.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);

> [!div class="nextstepaction"]
> [Атлас](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest):

Полные примеры кода см. в следующих статьях:

> [!div class="nextstepaction"]
> [Добавление маркера](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Добавление всплывающего окна](./map-add-popup.md)
