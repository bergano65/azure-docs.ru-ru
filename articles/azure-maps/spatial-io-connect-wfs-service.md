---
title: Подключение к службе веб-функций (WFS) (WFS) Карты Microsoft Azure
description: Узнайте, как подключиться к службе WFS, а затем задать запрос службы WFS с помощью веб-SDK Azure Maps и модуля Spatial IO.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8b511395eb61e8845aaa11e5ca7a490dc461424d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334204"
---
# <a name="connect-to-a-wfs-service"></a>Подключение к службе WFS

Веб-служба функций (WFS) — это веб-сервис для запросов пространственных данных с стандартизированным API, определяемым Открытым геопространственным консорциумом (OGC). Класс `WfsClient` в пространственном модуле IO позволяет разработчикам подключаться к службе WFS и запросу данных службы.

Следующие функции поддерживаются классом: `WfsClient`

- Поддерживаемые `1.0.0`версии: , `1.1.0`и`2.0.0`
- Поддерживаемые операторы фильтров: двоичные сравнения, логика, математика, значение и `bbox`.
- Запросы делаются только с использованием. `HTTP GET`
- Поддерживаемые операции:

    | | |
    | :-- | :-- |
    | GetCapabilities | Создает документ метаданных с действительными операциями и параметрами WFS |
    | GetFeature | Возвращает выбор функций из источника данных |
    | ОпишитеFeatureType | Возвращает поддерживаемые типы функций |

## <a name="using-the-wfs-client"></a>Использование клиента WFS

Класс `atlas.io.ogc.WfsClient` в пространственном модуле IO позволяет легко запрашивать службу WFS и преобразовывать ответы в объекты GeoJSON. Этот объект GeoJSON может быть использован для других целей отображения.

Следующий код запрашивает службу WFS и отображает возвращенные функции на карте.

<br/>

<iframe height='700' scrolling='no' title='Простой пример WFS' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Смотрите пример Pen <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>Simple WFS</a> по<a href='https://codepen.io/azuremaps'>@azuremaps</a>картам Azure () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Поддерживаемые фильтры

Спецификация стандарта WFS использует фильтры OGC. Фильтры ниже поддерживаются клиентом WFS, предполагая, что служба, называемая также поддерживает эти фильтры. Пользовательские строки фильтра `CustomFilter` могут быть переданы в класс.

**Логические операторы**

- `And`
- `Or`
- `Not`

**Операторы стоимости**

- `GmlObjectId`
- `ResourceId`

**Операторы математики**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Операторы сравнения**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

Следующий код демонстрирует использование различных фильтров с клиентом WFS.

<br/>

<iframe height='500' scrolling='no' title= 'Примеры фильтров WFS' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Смотрите <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>примеры фильтров</a> Pen WFS<a href='https://codepen.io/azuremaps'>@azuremaps</a>по картам Azure Maps () на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>Исследователь сервиса WFS

Следующий код использует клиента WFS для изучения служб WFS. Выберите слой типа свойств в службе и просмотрите связанную с ней легенду.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'Исследователь сервиса WFS' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Смотрите <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>Pen WFS исследователь службы</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>По Azure Maps ( ) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

Для доступа к службам WFS, размещенным на конечных точках, не `proxyService` включенных CORS, прокси-сервис с включенным CORS может быть передан в опцию клиента WFS, как показано ниже. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Кредитное использование основных операций](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Детали формата поддерживаемых данных](spatial-io-supported-data-format-details.md)
