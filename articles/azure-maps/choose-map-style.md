---
title: Функции стиля карт в службе Azure Maps | Документация Майкрософт
description: Сведения о функциях стиля в службе Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: eb667c398be0bd51e05a6b65d416d5bce54e4386
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881969"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Выбор стиля карты в службе Azure Maps

Многие [поддерживаемые стили карт в Azure Maps](./supported-map-styles.md) доступны в веб-пакете SDK. В этой статье показано, как с помощью связанных со стилем функций установить стиль при загрузке карты, установить новый стиль и использовать элемент выбора стиля.

## <a name="set-style-on-map-load"></a>Задание стилей при загрузке карты

В следующем коде `style` параметру Map присваивается `grayscale_dark` значение on Initialization.

<br/>

<iframe height='500' scrolling='no' title='Установка стиля при загрузке карты' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Установка стиля при загрузке карты</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="update-the-style"></a>Обновление стиля

В следующем коде после загрузки экземпляра Map стиль схемы обновляется с `road` на `satellite` с помощью функции [SetStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) на карте.

<br/>

<iframe height='500' scrolling='no' title='Обновление стиля' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Обновление стиля</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker"></a>Добавление средства выбора стиля

Следующий код добавляет [стилеконтрол](/javascript/api/azure-maps-control/atlas.control.stylecontrol) к карте, чтобы пользователь мог легко переключаться между различными стилями карт. 

<br/>

<iframe height='500' scrolling='no' title='Добавление элемента выбора стиля' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Добавление элемента выбора стиля</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> По умолчанию элемент управления Выбор стиля перечисляет все стили, доступные при использовании ценовой категории S0 Azure Maps по умолчанию. Если требуется сократить число стилей в этом списке, передайте массив стилей, которые должны отображаться в списке, в `mapStyle` параметр выбора стиля. Если вы используете S1 и хотите отобразить все доступные стили, задайте `mapStyles` для `"all"`параметра Выбор стиля значение.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о классах и методах, используемых в этой статье.

> [!div class="nextstepaction"]
> класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);

Добавление элементов управления в карты:

> [!div class="nextstepaction"]
> [Добавление элементов управления картой в службе Azure Maps](map-add-controls.md)

> [!div class="nextstepaction"]
> [Добавление маркера](map-add-pin.md)
