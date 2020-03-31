---
title: Добавить открытый слож карты Геопространственного Консорциума (OGC) Карты Microsoft Azure
description: Узнайте, как наложить слой карты OGC на карту и как использовать различные параметры в классе OgcMapLayer.
author: philmea
ms.author: philmea
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b753ecfc07cfb3806838f8a05dbe33ef0bb92730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334283"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Добавьте слой карты из Открытого геопространственного консорциума (OGC)

Класс `atlas.layer.OgcMapLayer` может накладывать изображения Web Map Services (WMS) и веб-карты Tile Services (WMTS) на карте. WMS является стандартным протоколом, разработанным OGC для обслуживания геоссылки изображения карты через Интернет. Геоссылка изображений — это процессы привязки изображения к географическому местоположению. WMTS также является стандартным протоколом, разработанным OGC. Он предназначен для обслуживания предварительно оказанных и геоссылки картплитки.

В следующих разделах описаны функции `OgcMapLayer` службы веб-карт, которые поддерживаются классом.

**Служба веб-карт (WMS)**

- Поддерживаемые `1.0.0`версии: , `1.1.0`, `1.1.1`и`1.3.0`
- Служба должна поддерживать `EPSG:3857` проекционную систему или обрабатывать репроекции.
- GetFeatureInfo требует, чтобы `EPSG:4326` служба поддерживала или обрабатывала репроекции. 
- Поддерживаемые операции:

    | | |
    | :-- | :-- |
    | GetCapabilities | Получение метаданных об услуге с поддерживаемыми возможностями |
    | GetMap | Извлекает изображение карты для определенного региона |
    | GetFeatureInfo | Извлекает, `feature_info`который содержит базовые данные о функции |

**Веб-карта плитки службы (WMTS)**

- Поддерживаемые версии:`1.0.0`
- Плитки должны быть квадратными, такими, чтобы. `TileWidth == TileHeight`
- CRS поддерживается: `EPSG:3857` или`GoogleMapsCompatible` 
- Идентификатор TileMatrix должен быть универсальным значением, которое соответствует уровню масштабирования на карте. На лазурной карте уровень зума `"0"` является `"22"`значением между и . Таким `"0"` образом, поддерживается, но `"00"` не поддерживается.
- Поддерживаемые операции:

    | | |
    | :-- | :-- |
    | GetCapabilities | Извлекает поддерживаемые операции и функции |
    | GetTile | Извлекает изображения для конкретной плитки |

## <a name="overlay-an-ogc-map-layer"></a>Наложение слоя карты OGC

Базовым `url` URL-адресом службы может быть или полный URL-адрес с запросом для получения возможностей службы. В зависимости от предоставленных деталей клиент WFS может попробовать несколько стандартных форматов URL, чтобы определить, как получить доступ к службе.

Следующий код показывает, как наложить слой карты OGC на карте.

<br/>

<iframe height='700' scrolling='no' title='Пример слоя карты OGC' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Смотрите <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>пример слоя Pen OGC Map</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>по картам Azure Maps () на <a href='https://codepen.io'>CodePen.</a>
</iframe>

## <a name="ogc-map-layer-options"></a>Параметры слоя карты OGC

Ниже приведена выборка, демонстрирующие различные параметры слоя карты OGC. Вы можете нажать на кнопку ручки кода в правом верхнем углу, чтобы отсеивать кодовое перо.

<br/>

<iframe height='700' scrolling='no' title='Параметры слоя карты OGC' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Смотрите <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>параметры слоя карты Pen OGC</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>по картам Azure Maps () на <a href='https://codepen.io'>CodePen.</a>
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC Веб-Карта Службы исследователь

Следующие инструменты накладывают изображения из служб Web Map Services (WMS) и Web Map Tile Services (WMTS) в слои. Можно выбрать, какие слои в службе отображаются на карте. Вы также можете просмотреть связанные легенды для этих слоев.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='OGC Веб-Карта Службы исследователь' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Смотрите <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>Pen OGC Web Map Service</a> Explorer<a href='https://codepen.io/azuremaps'>@azuremaps</a>по Azure Maps ( ) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

Вы также можете указать настройки карты для использования прокси-сервиса. Прокси-сервис позволяет загружать ресурсы, размещенные на доменах, в которых нет CORS включен.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Смотрите следующие статьи, которые содержат образцы кода, которые можно добавить на свои карты:

> [!div class="nextstepaction"]
> [Подключение к службе WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Кредитное использование основных операций](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Детали формата поддерживаемых данных](spatial-io-supported-data-format-details.md)
