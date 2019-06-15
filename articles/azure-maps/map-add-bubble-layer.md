---
title: Добавление слоя пузырьков в Azure Maps | Документация Майкрософт
description: Сведения о добавлении слоя пузырьков на карту Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f2c4c6b8655d5efb993a2dedf536000ac94328c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60769691"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Добавление слоя пузырьков на карту

В этой статье показано, как точки данных из источника могут преобразоваться для просмотра в виде слоя пузырьков на карте. Слои пузырьков преобразовывают точки в круги на карте с помощью фиксированного значения радиуса в пикселях. 

> [!TIP]
> Слои пузырьков по умолчанию отображают координаты всех геометрических объектов в источнике данных. Чтобы ограничить на уровне, таким образом, чтобы он отображает только точка geometry компоненты набора `filter` свойства слоя, чтобы `['==', ['geometry-type'], 'Point']` или `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` Если вы хотите включить также MultiPoint функции.

## <a name="add-a-bubble-layer"></a>Добавление слоя пузырьков

<iframe height='500' scrolling='no' title='Источник данных BubbleLayer' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Посмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>Источник данных BubbleLayer</a> от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода определяется массив объектов [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) и добавляется к объекту [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest).

В [слое пузырьков](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [источника данных](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются к просмотру как круги на карте. В последнем блоке кода создается слой пузырьков, который добавляется на карту. Просмотрите свойства слоя пузырьков в разделе о классе [BubblerLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Массив объектов Point, источник данных и слой пузырьков создаются и добавляются на карту с помощью функции [прослушивателя событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Это обеспечивает отображение кругов только после полной загрузки карты.

## <a name="show-labels-with-a-bubble-layer"></a>Отображение меток с помощью слоя пузырьков

<iframe height='500' scrolling='no' title='Источник данных MultiLayer' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Посмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>Источник данных MultiLayer</a> от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

Приведенный выше код показывает, как визуализировать и пометить данные на карте. В первом блоке кода, приведенном выше, создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создается объект [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest). Затем создается объект источника данных с помощью класса [источника данных](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) и добавляется точка в источник данных.

В [слое пузырьков](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [источника данных](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются к просмотру как круги на карте. В третьем блоке кода создается слой пузырьков, который добавляется на карту. Просмотрите свойства слоя пузырьков в разделе о классе [BubblerLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

В [слое символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) данные на основе точек в оболочке [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) преобразовываются для просмотра в качестве символов на карте с помощью текста или значков. В последнем блоке кода создается и добавляется на карту слой символов, который преобразовывает текстовую метку для пузырька. Сведения о свойствах слоя символов см. в разделе [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

Источник данных и слои создаются и добавляются на карту с помощью функции [прослушивателя событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Это обеспечивает отображение данных только после полной загрузки карты.

## <a name="customize-a-bubble-layer"></a>Настройка слоя пузырьков

Слой пузырьков имеет несколько параметров стиля, которые можно опробовать с помощью представленного ниже средства.

<br/>

<iframe height='700' scrolling='no' title='Параметры слоя пузырьков' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Параметры слоя пузырьков</a> от Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Добавление слоя символов](map-add-pin.md)

> [!div class="nextstepaction"]
> [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)