---
title: Функции стиля карт в службе Azure Maps | Документация Майкрософт
description: Сведения о функциях стиля в службе Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 78e3f6a5f4a55fd4bf1925672205eb490e7c6b24
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695712"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Выбор стиля карты в службе Azure Maps

В службе Azure Maps доступно четыре разных стиля карт. Дополнительные сведения см. в статье [Стили карт, поддерживаемые в службе Azure Maps](./supported-map-styles.md). В этой статье показано, как с помощью связанных со стилем функций установить стиль при загрузке карты, установить новый стиль и использовать элемент выбора стиля.

## <a name="set-style-on-map-load"></a>Задание стилей при загрузке карты

<iframe height='500' scrolling='no' title='Установка стиля при загрузке карты' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Установка стиля при загрузке карты</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

Блок кода, приведенный выше, устанавливает ключ подписки и создает объект map с заданным стилем grayscale_dark. См. инструкции по [созданию карты](./map-create.md).

## <a name="update-the-style"></a>Обновление стиля

<iframe height='500' scrolling='no' title='Обновление стиля' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Обновление стиля</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

Блок кода, приведенный выше, устанавливает ключ подписки и создает объект map, предварительно не задавая стиль. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода использует метод карты [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest), чтобы установить стиль карты в качестве вспомогательного.

## <a name="add-the-style-picker"></a>Добавление средства выбора стиля

<iframe height='500' scrolling='no' title='Добавление элемента выбора стиля' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Добавление элемента выбора стиля</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

Первый блок приведенного выше кода устанавливает ключ подписки и создает объект map, задавая карте стиль grayscale_dark. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода создает селектор стиля с помощью атласа [StyleControl](/javascript/api/azure-maps-control/atlas.controls.stylecontrol).

Элемент выбора стиля позволяет выбрать стиль для карты. Третий блок кода добавляет элемент выбора стиля на карту с помощью метода карты [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). Селектор стиля находится в пределах **прослушивателя событий** карты для того, чтобы обеспечить его загрузку после полной загрузки карты.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, используемых в этой статье.

> [!div class="nextstepaction"]
> класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);

Добавление элемента управления на карты.

> [!div class="nextstepaction"]
> [Добавление элементов управления картой в службе Azure Maps](./map-add-controls.md)

Добавление ПИН-кода карты.

> [!div class="nextstepaction"]
> [Добавление маркера](./map-add-pin.md)
