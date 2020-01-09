---
title: Перенос веб-приложения из Google Maps | Документация Майкрософт
description: Руководство по переносу веб-приложения из Google Maps в Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a414d7b15f81ab81783b66f8297a207afe569365
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562176"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Перенос веб-приложения из Google Maps

Большинство веб-приложений, использующих карты Google, используют пакет SDK JavaScript для Google Maps v3. Веб-пакет SDK для Azure Maps — это подходящий пакет SDK на основе Azure для миграции. Веб-пакет SDK Azure Maps позволяет настраивать интерактивные карты, используя собственные изображения и содержимое для отображения в веб-приложениях или мобильных приложениях. Этот элемент управления использует WebGL, позволяя вам отображать большие наборы данных с высокой производительностью. Разрабатывайте с помощью этого пакета SDK, используя JavaScript или TypeScript.

При переносе существующего веб-приложения проверьте, использует ли он библиотеку элементов управления Map с открытым кодом, например Цесиум, Леафлет и Опенлайерс. Если это так, и вы не хотите использовать веб-пакет SDK для Azure Maps, еще один вариант миграции приложения — продолжить использовать элемент управления карты с открытым кодом и подключить его к Azure Mapsным службам плиток ([плитки](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [спутниковых плиток](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)). Ниже приведены сведения об использовании Azure Maps в некоторых часто используемых библиотеках элементов управления картой с открытым кодом.

- Цесиум — элемент управления "Трехмерная схема" для веб-сайта. [Образец кода](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Документация](https://cesiumjs.org/)
- Леафлет — упрощенный элемент управления 2D-картой для Интернета. [Образец кода](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Документация](https://leafletjs.com/)
- Опенлайерс — элемент управления 2D-картой для веб-сайта, который поддерживает проекции. [Образец кода](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Документация](https://openlayers.org/)

## <a name="key-features-support"></a>Поддержка основных функций

В следующей таблице перечислены ключевые функции API в пакете SDK для JavaScript в Google Maps v3 и поддержка аналогичного API в веб-пакете SDK для Azure Maps.

| Функция Google Maps     | Azure Maps поддержка веб-пакета SDK |
|-------------------------|:--------------------------:|
| Маркеры                 | ✓                          |
| Кластеризация маркеров       | ✓                          |
| Ломаные & многоугольники    | ✓                          |
| Уровни данных             | ✓                          |
| Наложение заземления         | ✓                          |
| Карты рисков               | ✓                          |
| Мозаичные слои             | ✓                          |
| Слой КМЛ               | ✓                          |
| Инструменты для рисования           | ✓                          |
| Служба геокодирования        | ✓                          |
| Служба "направления"      | ✓                          |
| Служба матрицы расстояний | ✓                          |
| Служба повышения прав       | Запланировано                    |

## <a name="notable-differences-in-the-web-sdks"></a>Важные различия в веб-пакетах SDK

Ниже приведены некоторые ключевые различия между Google Maps и Azure Maps веб-пакетами SDK, о которых следует знать:

- Помимо предоставления размещенной конечной точки для доступа к Azure Maps веб-пакету SDK, пакет NPM также доступен для внедрения веб-пакета SDK в приложения, если это предпочтительно. Дополнительные сведения см. в этой [документации](how-to-use-map-control.md) . Этот пакет также включает определения TypeScript.
- После создания экземпляра класса Map в Azure Maps код должен ждать срабатывания события Maps `ready` или `load`, прежде чем взаимодействовать с картой. Это обеспечит загрузку всех ресурсов карт и готовность к доступу.
- Обе платформы используют аналогичную систему мозаичного заполнения для базовых карт, однако плитки в Google Maps 256 пикселов в измерении, а плитки в Azure Maps — 512 пикселей в измерении. Таким образом, чтобы получить такое же представление карты в Azure Maps в виде Google Maps, уровень масштабирования, используемый в картах Google, должен быть вычтен одним из Azure Maps.
- Координаты в Google Maps называются "широтой, долготой", а Azure Maps использует "Долгота, Широта". Это соответствует стандартному `[x, y]`, за которым следуют большинство GIS платформ.
- Фигуры в веб-пакете SDK Azure Maps основаны на схеме геоjson. Вспомогательные классы предоставляются через [пространство имен *Atlas. Data* ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest). Также присутствует [*Atlas. Класс Shape*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) , который можно использовать для создания обертки для геообъектов JSON и упрощения их обновления и поддержки в виде привязки данных.
- Координаты в Azure Maps определяются как объекты положения, которые можно указать как простой массив чисел в формате `[longitude, latitude]` или New Atlas. Data. положением (Долгота, Широта).
    > [!TIP]
    > Класс положения имеет статический вспомогательный метод для импорта координат, которые находятся в формате "Широта, Долгота". Метод [Atlas. Data. Place. фромлатлнг](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) часто может быть заменен методом `new google.maps.LatLng` в коде Google Maps.
- Вместо того, чтобы указывать сведения о стилях для каждой фигуры, добавляемой на карту, Azure Maps отделяет стили от данных. Данные хранятся в источниках данных и подключены к слоям подготовки к просмотру, которые Azure Maps коде для визуализации данных. Такой подход обеспечивает повышенный выигрыш в производительности. Кроме того, многие слои поддерживают основанные на данных стили, в которых бизнес-логику можно добавить в параметры стиля слоя, которые изменяют способ отрисовки отдельных фигур в слое на основе свойств, определенных в фигуре.

## <a name="web-sdk-side-by-side-examples"></a>Примеры параллельного веб-пакета SDK

Ниже приведена Коллекция примеров кода для каждой платформы, охватывающих распространенные варианты использования, которые помогут вам перенести веб-приложение из пакета SDK для JavaScript версии 3 из Google Maps в веб-пакет SDK для Azure Maps. Примеры кода, связанные с веб-приложениями, предоставляются в JavaScript. Однако Azure Maps также предоставляет определения TypeScript в качестве дополнительного параметра через [модуль NPM](how-to-use-map-control.md).

### <a name="load-a-map"></a>Загрузка схемы

Загрузка схемы в обоих пакетах SDK выполняется в одном и том же наборе действий.

- Добавьте ссылку на пакет SDK для Map.
- Добавьте тег `div` в текст страницы, который будет служить заполнителем для схемы.
- Создайте функцию JavaScript, которая вызывается после загрузки страницы.
- Создайте экземпляр соответствующего класса Map.

**Некоторые ключевые отличия**

- Для использования Google Maps необходимо указать ключ учетной записи в справочнике по скриптам API. Учетные данные проверки подлинности для Azure Maps указываются в качестве параметров класса Map. Это может быть ключ подписки или сведения о Azure Active Directory.
- Google Maps принимает функцию обратного вызова в справочнике по скрипту API, который используется для вызова функции инициализации для загрузки карты. С Azure Maps следует использовать событие OnLoad страницы.
- При ссылке на элемент `div`, в котором будет выполняться визуализация карты, классу `Map` в Azure Maps требуется только `id` значение, тогда как для Google Maps требуется объект `HTMLElement`.
- Координаты в Azure Maps определяются как объекты положения, которые могут быть указаны в виде простого массива чисел в формате `[longitude, latitude]`.
- Уровень масштаба в Azure Maps меньше, чем пример Google Maps, из-за разницы в размерах системы заполнения между платформами.
- По умолчанию Azure Maps не добавляет элементы управления навигацией на холст, такие как кнопки масштабирования и кнопки стилей карт. Однако есть элементы управления для добавления средств выбора стиля карт, кнопок масштабирования, компаса или поворота, а также элемента управления "шаг".
- Обработчик событий добавляется в Azure Maps для отслеживания `ready` события экземпляра Map. Это сработает, когда на карте закончится загрузка контекста WebGL и всех необходимых ресурсов. В этот обработчик событий можно добавить любой код нагрузки после загрузки.

В приведенных ниже примерах показано, как загрузить базовую карту, которая выравнивается по центру в Нью-Йорке по координатам (Долгота:-73,985, Широта: 40,747) и находится на уровне масштабирования 12 в Google Maps.

**До: карты Google**

В следующем примере кода показано, как отобразить карту Google в центре и увеличить ее расположение.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

При выполнении этого кода в браузере отобразится схема, которая выглядит как на следующем рисунке:

<center>

![простых](media/migrate-google-maps-web-app/simple-google-map.png)</center>ных карт Google

**После: Azure Maps**

В следующем примере кода показано, как загрузить карту с тем же представлением в Azure Maps, а также с помощью элементов управления стиля карт и кнопок масштабирования.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

При выполнении этого кода в браузере отобразится схема, которая выглядит как на следующем рисунке:

<center>

![простой Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

Подробную документацию по настройке и использованию элемента управления картой Azure Maps в веб-приложении можно найти [здесь](how-to-use-map-control.md).

> [!NOTE]
> В отличие от Google Maps, Azure Maps не требует указания первоначального уровня центра и масштаба при загрузке карты. Если эти сведения не предоставляются при загрузке схемы, то на карте будет предпринята попытка определить, в каком городе находится пользователь, и будет ли он центрирован и масштабирует карту.

**Дополнительные ресурсы:**

- Azure Maps также предоставляет элементы управления навигацией для поворота и наклона представления карт, как описано [здесь](map-add-controls.md).

### <a name="localizing-the-map"></a>Локализация схемы

Если ваша аудитория распределена по нескольким странам или говорят на разных языках, локализация важна.

**До: карты Google**

Чтобы локализовать карты Google, параметры языка и региона добавляются в

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Ниже приведен пример использования Google Maps с языком, имеющим значение fr-FR.

<center>

![](media/migrate-google-maps-web-app/google-maps-localization.png)</center> локализации Google Maps

**После: Azure Maps**

Azure Maps предоставляет два разных способа настройки языкового и регионального представления для схемы. Первый вариант — Добавить эту информацию в глобальное пространство имен *Atlas* , что приведет к тому, что все экземпляры элементов управления картой в приложении будут по умолчанию иметь эти параметры. Следующий пример устанавливает для языка значение французский (fr-FR) и региональное представление "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Второй вариант — передать эту информацию в параметры карты при загрузке карты следующим образом:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> С Azure Maps можно загрузить несколько экземпляров карт на одной странице с разными языками и региональными параметрами. Кроме того, можно обновить эти параметры на карте после их загрузки. Подробный список поддерживаемых языков в Azure Maps можно найти [здесь](supported-languages.md).

Ниже приведен пример Azure Maps с языком, для которого задано значение fr, а для пользовательской области задано значение fr-FR.

<center>

![](media/migrate-google-maps-web-app/azure-maps-localization.png)</center> локализации Azure Maps

### <a name="setting-the-map-view"></a>Настройка представления карт

Динамические карты в Azure и Google Maps можно программно перемещать в новые географические расположения путем вызова соответствующих функций в JavaScript. В приведенных ниже примерах показано, как сделать так, чтобы карта отображала вспомогательные аэрофотосъемки изображения, Центрировать карту по расположению с координатами (Долгота:-111,0225, Широта: 35,0272) и измените уровень масштабирования на 15 в Google Maps.

> [!NOTE]
> В Google Maps используются плитки 256 пикселей в измерениях, а в Azure Maps используется плитка размером более 512 пикселей. Это сокращает количество сетевых запросов, необходимых, Azure Maps для загрузки той же области карты, что и Google Maps. Тем не менее, из-за того, что Пирамидальная диаграмма работает в элементах управления карты, крупные плитки в Azure Maps означает, что для достижения этой же видимой области в качестве карты в Google Maps необходимо вычесть масштаб, используемый в Google Maps, на единицу при использовании Azure Maps.

**До: карты Google**

Элемент управления карты Google Maps можно переместить программным способом с помощью метода `setOptions`, который позволяет указать центр карты и уровень масштабирования.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![представление набора Google Maps](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**После: Azure Maps**

В Azure Maps можно изменить расположение Map программным способом с помощью метода `setCamera` на карте, и изменение стиля схемы будет изменено с помощью метода `setStyle`. Обратите внимание, что координаты в Azure Maps находятся в формате "Долгота, Широта", а значение масштаба вычитается на единицу.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![представление набора Azure Maps](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**Дополнительные ресурсы:**

- [Выбор стиля карты](choose-map-style.md)
- [Поддерживаемые стили карт](supported-map-styles.md)

### <a name="adding-a-marker"></a>Добавление маркера

В Azure Maps существует несколько способов, с помощью которых данные точек можно визуализировать на карте.

- **Маркеры HTML** — отрисовывает точки с помощью традиционных элементов DOM. Маркеры HTML поддерживают перетаскивание.
- **Слой символов** — отрисовывает точки с значком и (или) текстом в контексте WebGL.
- **Пузырьковый слой** — отрисовывает точки как круги на карте. Радиусы кругов можно масштабировать на основе свойств данных.

Слои "символ" и "пузырьковая" отображаются в контексте WebGL и могут визуализировать очень большие наборы точек на карте. Для этих слоев данные должны храниться в источнике данных. Источники данных и слои подготовки отчетов должны быть добавлены на карту после запуска события `ready`. Маркеры HTML подготавливаются к просмотру как элементы DOM на странице и не используют источник данных. Чем больше элементов DOM содержит страница, тем медленнее будет страница. При отрисовке более чем нескольких сотен точек на карте рекомендуется использовать вместо него один из слоев подготовки к просмотру.

В следующих примерах на карту добавляется маркер в (Долгота:-0,2, Широта: 51,5) с числом 10, наложенным в качестве метки.

**До: карты Google**

При использовании Google Maps маркеры добавляются в карту с помощью класса `google.maps.Marker` и путем указания карты в качестве одного из параметров.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

](media/migrate-google-maps-web-app/google-maps-marker.png)</center> маркера ![Google Maps

**После: Azure Maps с помощью маркеров HTML**

В Azure Maps маркеры HTML можно использовать для отображения точки на карте и рекомендуется для работы только с приложениями, которые должны отображать небольшое количество точек на карте. Чтобы использовать HTML-маркер, просто создайте экземпляр класса `atlas.HtmlMarker`, задайте параметры текста и расположения, а затем добавьте маркер в карту с помощью метода `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps маркер HTML](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**После: Azure Maps с использованием слоя символов**

При использовании слоя символов данные должны быть добавлены к источнику данных и к источнику данных, присоединенному к слою. Кроме того, источник данных и слой должны быть добавлены на карту после запуска события `ready`. Чтобы отобразить уникальное текстовое значение над символом, Текстовая информация должна храниться как свойство точки данных и это свойство, на которое ссылается параметр `textField` слоя. Это немного больше работы, чем использование маркеров HTML, но предоставляет множество преимуществ для повышения производительности.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Mapsого уровня символов](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**Дополнительные ресурсы:**

- [Создание источника данных](create-data-source-web-sdk.md)
- [Добавление слоя символов](map-add-pin.md)
- [Добавить пузырьковый слой](map-add-bubble-layer.md)
- [Данные точки кластера](clustering-point-data-web-sdk.md)
- [Добавление маркеров HTML](map-add-custom-html.md)
- [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)
- [Параметры значков на уровне символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Параметр "текст на уровне символов"](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Класс маркера HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Параметры маркера HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Добавление пользовательского маркера

Пользовательские образы можно использовать для представления точек на карте. На следующем рисунке в приведенных ниже примерах используется пользовательский образ для отображения точки на карте в (Широта: 51,5, Долгота:-0,2) и смещение позиции маркера таким образом, чтобы точка значка канцелярской точки совпадала с правильной позицией на карте.

<center>

![изображение желтой канцелярской кнопки](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
илв\_ая кнопка. png</center>

**До: карты Google**

В Google Maps пользовательский маркер создается путем указания объекта `Icon`, содержащего `url` к изображению, `anchor` точки для выравнивания точки изображения канцелярской кнопки с координатой на карте. Значение привязки в Google Maps относительно левого верхнего угла изображения.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

<center>

](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center> настраиваемый маркер ![Google Maps

**После: Azure Maps с помощью маркеров HTML**

Чтобы настроить HTML-маркер в Azure Maps `string` HTML или `HTMLElement` можно передать в параметр `htmlContent` маркера. В Azure Maps параметр `anchor` используется для указания относительной позиции маркера относительно координаты позиции с помощью одного из девяти определенных опорных точек; «Center», «Top», «Bottom», «Left», «Right», «сверху слева», «сверху справа», «снизу слева», «снизу справа». Содержимое привязывается к нижнему центру содержимого HTML по умолчанию. Чтобы упростить перенос кода из Google Maps, установите `anchor` в значение "сверху слева", а затем используйте параметр `pixelOffset` с тем же смещением, которое использовалось в Google Maps. Смещение в Azure Maps перемещается в противоположном направлении в Google Maps, поэтому умножьте их на минус единицу.

> [!TIP]
> Добавьте `pointer-events:none` в качестве стиля HTML-содержимого, чтобы отключить поведение перетаскивания по умолчанию в Microsoft ребро, при котором будет отображаться нежелательный значок.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps пользовательского HTML-маркера](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**После: Azure Maps с использованием слоя символов**

Слои символов в Azure Maps поддерживают также пользовательские образы, но сначала необходимо загрузить образ в карту ресурсов и назначить уникальный идентификатор. Затем слой символов может ссылаться на этот идентификатор. Символ может быть смещен для согласования с нужной точкой на изображении с помощью значка `offset`. В Azure Maps параметр `anchor` используется для указания относительной позиции символа относительно координаты позиции с помощью одного из девяти определенных опорных точек; «Center», «Top», «Bottom», «Left», «Right», «сверху слева», «сверху справа», «снизу слева», «снизу справа». Содержимое привязывается к нижнему центру содержимого HTML по умолчанию. Чтобы упростить перенос кода из Google Maps, установите `anchor` в значение "сверху слева", а затем используйте параметр `offset` с тем же смещением, которое использовалось в Google Maps. Смещение в Azure Maps перемещается в противоположном направлении в Google Maps, поэтому умножьте их на минус единицу.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps пользовательского значка](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> Чтобы создать расширенную пользовательскую отрисовку точек, используйте одновременно несколько слоев отрисовки. Например, если требуется несколько признаков с одинаковым значком на разных цветных кругах, вместо создания набора изображений для каждого цвета перекрытие слоя символов поверх пузырькового слоя и их ссылки на один и тот же источник данных. Это будет гораздо эффективнее, чем создание, и наличие в карте набора различных изображений.

**Дополнительные ресурсы:**

- [Создание источника данных](create-data-source-web-sdk.md)
- [Добавление слоя символов](map-add-pin.md)
- [Добавление маркеров HTML](map-add-custom-html.md)
- [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)
- [Параметры значков на уровне символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Параметр "текст на уровне символов"](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Класс маркера HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Параметры маркера HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Добавление ломаной линии

Ломаные линии используются для представления линий или траекторий на карте. В следующих примерах показано, как создать пунктирную ломаную линию на карте.

**До: карты Google**

В Google Maps класс ломаной линии принимает набор параметров. Массив координат передается в параметре `path` ломаной линии.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

<center>

![](media/migrate-google-maps-web-app/google-maps-polyline.png)</center> ломаной линии Google Maps

**После: Azure Maps**

В Azure Maps ломаные объекты называются объектами LineString или MultiLineString. Эти объекты можно добавлять в источник данных и подготавливать к просмотру с помощью слоя линий.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![Azure Maps Ломаная линия](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**Дополнительные ресурсы:**

- [Добавление линий к сопоставлению](map-add-line-layer.md)
- [Параметры слоя линий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Добавление многоугольника

Многоугольники используются для представления области на карте. Azure Maps и Google Maps обеспечивают очень похожую поддержку многоугольников. В следующих примерах показано, как создать многоугольник, который образует треугольник на основе координаты центральной координаты на карте.

**До: карты Google**

В Google Maps класс Polygon принимает набор параметров. Массив координат передается в параметре `paths` многоугольника.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

<center>

![](media/migrate-google-maps-web-app/google-maps-polygon.png)</center> многоугольников Google Maps

**После: Azure Maps**

В Azure Maps объекты многоугольников и многоугольных объектов можно добавить в источник данных и визуализировать на карте с помощью слоев. Площадь многоугольника может быть визуализирована на слое многоугольников. Контур многоугольника можно визуализировать с помощью слоя линий.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![Azure Maps многоугольник](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**Дополнительные ресурсы:**

- [Добавление многоугольника к сопоставлению](map-add-shape.md)
- [Добавление окружности к карте](map-add-shape.md#add-a-circle-to-the-map)
- [Параметры слоя многоугольников](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Параметры слоя линий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Отображение окна сведений

Дополнительные сведения для сущности можно отобразить на карте как класс `google.maps.InfoWindow` в Google Maps, в Azure Maps это можно сделать с помощью класса `atlas.Popup`. В следующих примерах на карту добавляется маркер, а при щелчке отображается окно сведений или всплывающее меню.

**До: карты Google**

При использовании Google Maps окно сведений создается с помощью конструктора `google.maps.InfoWindow`.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

<center>

всплывающее окно ![Google Maps](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**После: Azure Maps**

В Azure Maps всплывающее окно можно использовать для вывода дополнительных сведений о расположении. Объект HTML `string` или `HTMLElement` можно передать в параметр `content` всплывающего окна. Всплывающие окна могут отображаться независимо от любой фигуры, если это необходимо, и, таким образом, требовать указания значения `position`. Чтобы отобразить всплывающее окно, вызовите метод `open` и передайте `map`, в котором должно отображаться всплывающее окно.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

всплывающее окно Azure Maps ![](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> Чтобы сделать то же самое с символом, пузырьковой, линией или слоем многоугольника, просто передайте слой в код события Maps вместо маркера.

**Дополнительные ресурсы:**

- [Добавление всплывающего окна](map-add-popup.md)
- [Всплывающее окно с медиа содержимым](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Всплывающие окна на фигурах](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Повторное использование всплывающего окна с несколькими ПИН-контактами](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Класс Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Параметры всплывающего окна](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>Импорт геофайлового файла JSON

Google Maps поддерживает загрузку и динамическую стилизацию данных геоjson с помощью класса `google.maps.Data`. Функциональность этого класса обеспечивает гораздо больше возможностей, чем основанное на данных стиле Azure Maps. Одно из ключевых различий заключается в том, что в Google Maps вы указали функцию обратного вызова и бизнес-логику для определения стиля каждого компонента, который она обрабатывает отдельно в потоке пользовательского интерфейса. В Azure Maps слои поддерживают указание выражений, управляемых данными, в виде параметров стилей. Эти выражения обрабатываются во время визуализации в отдельном потоке и обеспечивают повышенную производительность отрисовки и позволяют быстрее подготавливать к просмотру большие наборы данных.

В следующих примерах загружается веб-канал геоjson всех землетрясения за последние семь дней с USGS и отображаются в виде масштабируемых кругов на карте. Цвет и масштаб каждого круга основаны на величине каждого землетрясения, который хранится в свойстве `"mag"` каждого компонента в наборе данных. Если величина больше или равна пяти, окружность будет красной, если больше или равно трем, но меньше пяти, окружность будет оранжевымся зеленым цветом. Радиус каждого круга будет экспонентой величины, умноженной на 0,1.

**До: карты Google**

В Google Maps одна функция обратного вызова может быть указана в методе `map.data.setStyle`, который будет использоваться для применения бизнес-логики к каждому компоненту, загруженному из геодоступного канала JSON через метод `map.data.loadGeoJson`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![геоjson](media/migrate-google-maps-web-app/google-maps-geojson.png)</center> карт Google

**После: Azure Maps**

Геоjson является базовым типом данных в Azure Maps и может легко импортироваться в источник данных с помощью метода `datasource.importFromUrl`. Пузырьковый слой предоставляет функции для отрисовки масштабируемых кругов на основе свойств компонентов в источнике данных. Вместо функции обратного вызова бизнес-логика преобразуется в выражение и передается в параметры стиля. Выражения определяют, как работает бизнес-логика, чтобы ее можно было передавать в другой поток и оценивать по данным функций. К Azure Maps можно добавлять несколько источников данных и слоев, каждый из которых имеет разную бизнес-логику, тем самым позволяя отображать несколько наборов данных на карте разными способами.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center> геоjson

**Дополнительные ресурсы:**

- [Добавление слоя символов](map-add-pin.md)
- [Добавить пузырьковый слой](map-add-bubble-layer.md)
- [Данные точки кластера](clustering-point-data-web-sdk.md)
- [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Кластеризация маркеров

При визуализации большого количества точек данных на карте точки перекрываются, а схема выглядит недостаточной и ее можно будет увидеть и использовать. Кластеризацию точечных данных можно использовать для повышения удобства работы пользователей, а также для повышения производительности. Данные точки кластеризации — это процесс объединения данных точек, расположенных рядом друг с другом и представляющих их на карте как единую кластеризованную точку данных. По мере того, как пользователь масштабирует карту, кластеры разбиваются на отдельные точки данных.

В следующих примерах загружается веб-канал геоjson данных землетрясения из прошлой недели и добавляется в карту. Кластеры подготавливаются к просмотру как масштабируемые и цветные кружки в зависимости от количества содержащихся в них точек.

> [!NOTE]
> Существует несколько различных алгоритмов, используемых для кластеризации маркеров. Google и Azure Maps используют несколько разных алгоритмов. Таким образом, иногда распределение точек в кластерах может отличаться.

**До: карты Google**

В маркерах Google Maps можно выполнять кластеризацию, загрузив в библиотеку Маркерклустерер. Значки кластера ограничены изображениями, которые имеют числа от 1 до пяти в качестве их имени и размещаются в одном каталоге.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![](media/migrate-google-maps-web-app/google-maps-clustering.png)</center> кластеризации Google Maps

**После: Azure Maps**

В Azure Maps данные добавляются и управляются источником данных. Слои подключаются к источникам данных и визуализируют данные в них. Класс `DataSource` в Azure Maps предоставляет несколько вариантов кластеризации.

- `cluster` — указывает источнику данных на данные точки кластера.
- `clusterRadius` — радиус в точках на кластерные точки.
- `clusterMaxZoom` — максимальный уровень масштаба, в котором происходит кластеризация. Если увеличить масштаб более, все точки подготавливаются к просмотру как символы.
- `clusterProperties` — определяет пользовательские свойства, которые рассчитываются с помощью выражений для всех точек в каждом кластере и добавляются в свойства каждой точки кластера.

Если кластеризация включена, источник данных будет отсылать кластеризованные и некластеризованные точки данных слоям для подготовки к просмотру. Источник данных способен выполнять кластеризацию сотен тысяч точек данных. Кластеризованная точка данных имеет следующие свойства.

| Имя свойства             | Тип    | Description   |
|---------------------------|---------|---------------|
| `cluster`                 | Логическое | Указывает, представляет ли компонент кластер. |
| `cluster_id`              | string  | Уникальный идентификатор кластера, который можно использовать с методами `getClusterExpansionZoom`DataSource, `getClusterChildren`и `getClusterLeaves`. |
| `point_count`             | number  | Число точек, содержащихся в кластере.  |
| `point_count_abbreviated` | string  | Строка, которая сокращает значение `point_count`, если оно длинное. (например, 4 000 преобразуется в 4 КБ)  |

Класс `DataSource` имеет следующую вспомогательную функцию для доступа к дополнительным сведениям о кластере с помощью `cluster_id`.

| Метод | Возвращаемый тип | Description |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Promise&lt;Array&lt;функция&lt;Geometry, любая&gt; \| форма&gt; | Извлекает дочерние элементы заданного кластера на следующем уровне масштабирования. Эти дочерние элементы могут быть сочетанием фигур и подкластеров. Подкластеры будут содержать функции со свойствами, соответствующими Клустередпропертиес. |
| `getClusterExpansionZoom(clusterId: number)` | Номер&lt;Promise&gt; | Вычисляет уровень масштабирования, при котором кластер начинает разворачиваться или разбиваться на части. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Promise&lt;Array&lt;функция&lt;Geometry, любая&gt; \| форма&gt; | Извлекает все точки в кластере. Задайте `limit`, чтобы вернуть подмножество точек, и используйте `offset`, чтобы пролистать точки. |

При подготовке к просмотру кластеризованных данных на карте часто бывает проще использовать два или более слоя. В следующем примере используются три слоя — пузырьковый слой для рисования масштабируемых цветных кругов на основе размера кластеров, уровня символов для отрисовки размера кластера в виде текста и второго уровня символов для визуализации некластеризованных точек. Существует множество других способов отображения кластеризованных данных в Azure Maps, выделенных в документации по [данным точки кластера](clustering-point-data-web-sdk.md) .

Данные геоjson можно импортировать непосредственно в Azure Maps с помощью функции `importDataFromUrl` в классе `DataSource`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![кластеризация Azure Maps](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**Дополнительные ресурсы:**

- [Добавление слоя символов](map-add-pin.md)
- [Добавить пузырьковый слой](map-add-bubble-layer.md)
- [Данные точки кластера](clustering-point-data-web-sdk.md)
- [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Добавление тепловой карт

Тепловая карта, которую также называют картой точек плотности, это тип визуализации данных для представления плотности данных с помощью диапазона цветов. Такие визуализации часто используются для отображения "горячих точек" данных на карте, и они особенно полезны, чтобы преобразовывать для просмотра набор данных больших точек.

В следующих примерах загружается веб-канал геоjson всех землетрясения за прошлый месяц из USGS и отображаются в виде взвешенной тепловой карте, где в качестве веса используется свойство `"mag"`.

**До: карты Google**

Чтобы создать тепловую карту в Google Maps, необходимо загрузить библиотеку "Визуализация", добавив `&libraries=visualization` в URL-адрес скрипта API. Слой тепловой карты в Google Maps не поддерживает непосредственное преобразование данных в формате JSON, а вместо этого данные сначала необходимо скачать и преобразовать в массив взвешенных точек данных.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center> тепловой карты Google Maps

**После: Azure Maps**

В Azure Maps загрузить данные геоjson в источник данных и подключить источник данных к слою тепловой карт. Свойство, которое будет использоваться для веса, может быть передано в параметр `weight` с помощью выражения. Данные геоjson можно импортировать непосредственно в Azure Maps с помощью функции `importDataFromUrl` в классе `DataSource`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps тепловой карте](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**Дополнительные ресурсы:**

- [Добавление слоя тепловой карт](map-add-heat-map-layer.md)
- [Класс уровня тепловой карт](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Параметры слоя тепловой карт](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Наложение мозаичного слоя

Мозаичные слои, также известные как наложение изображений в Google Maps, позволяют накладывать большие изображения, разбитые на небольшие мозаичные изображения, которые соответствуют системе разбиения на карты. Это распространенный способ наложения больших изображений или очень больших наборов данных.

В приведенных ниже примерах накладывается мозаичный слой погоды из Айова окружающей среды Месонет Айова штата университета.

**До: карты Google**

В Google Maps мозаичные слои можно создавать с помощью класса `google.maps.ImageMapType`.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

![](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center> мозаичного слоя Google Maps

**После: Azure Maps**

В Azure Maps мозаичный слой может быть добавлен на карту во многом так же, как и любой другой слой. Форматированный URL-адрес с заполнителями x, y, масштаба; `{x}`, `{y}`, `{z}` соответственно, используется для указания уровню доступа к плиткам. Azure Maps уровни плитки также поддерживают заполнители `{quadkey}`, `{bbox-epsg-3857}` и `{subdomain}`.

> [!TIP]
> В Azure Maps слои можно легко отобразить под другими слоями, в том числе с базовыми слоями карт. Часто желательно отображать мозаичные слои под метками карты, чтобы их можно было легко читать. Метод `map.layers.add` принимает второй параметр, который представляет собой идентификатор слоя, в который следует вставить новый слой. Чтобы вставить мозаичный слой под метками карты, можно использовать следующий код: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure Maps мозаичного слоя](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> Запросы на плитку можно записать с помощью параметра `transformRequest` карты. Это позволит вам при необходимости изменить или добавить заголовки в запрос.

**Дополнительные ресурсы:**

- [Добавить мозаичные слои](map-add-tile-layer.md)
- [Класс мозаичного слоя](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Параметры мозаичного слоя](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Отображение данных дорожного движения

Данные трафика можно использовать как в Azure, так и в Google Maps.

**До: карты Google**

В данных трафика Google Maps карту можно перекрыть с помощью уровня трафика.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![](media/migrate-google-maps-web-app/google-maps-traffic.png)</center> трафика Google Maps

**После: Azure Maps**

Azure Maps предоставляет несколько различных параметров для отображения трафика. Инциденты трафика, такие как замыкания на пути и всякое, могут отображаться в виде значков на карте. Поток трафика, Раздельная цветовая кодировка может быть наложен на карту, а цвета можно изменить, чтобы они были основаны относительно предельной скорости, относительно нормальной ожидаемой задержки или абсолютной задержки. Данные об инцидентах в Azure Maps обновляются каждую минуту и потоки данных каждые две минуты.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure Maps трафика](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Если щелкнуть один из значков трафика в Azure Maps, дополнительные сведения отображаются во всплывающем окне.

<center>

![инцидента трафика Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**Дополнительные ресурсы:**

- [Отображение данных дорожного движения на карте](map-show-traffic.md)
- [Параметры оверлея трафика](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Добавление земельного наложения

Azure и Google Maps поддерживают перерасположение изображений с геоссылками на карте, чтобы они перемещаются и масштабируются по мере сдвига и масштабирования карты. В Google Maps они называются наложением заземления в Azure Maps они называются слоями изображения. Они отлично подходят для создания планов этажей, наложения старых карт или изображений из помощью дронов.

**До: карты Google**

При создании наложения на землю в Google Maps необходимо указать URL-адрес изображения для наложения и ограничивающий прямоугольник для привязки изображения к карте. Этот пример накладывает изображение на карту [Ньюарке New Джерси с 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) на карте.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

При выполнении этого кода в браузере отобразится схема, которая выглядит как на следующем рисунке:

<center>

![наложение образов Google Maps](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**После: Azure Maps**

В Azure Maps изображения с геоссылками можно перекрыть с помощью класса `atlas.layer.ImageLayer`. Этот класс требует URL-адрес изображения и набор координат для четырех углов изображения. Образ должен размещаться либо в том же домене, либо с включенной поддержкой CORs.

> [!TIP]
> Если у вас есть только север, Южный, Восток, Западная и поворот, а не координаты для каждого угла изображения, можно использовать статический метод [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) .

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![наложение образа Azure Maps](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**Дополнительные ресурсы:**

- [Наложение изображения](map-add-image-layer.md)
- [Класс уровня изображения](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>Дополнительные примеры кода

Ниже приведены некоторые дополнительные примеры кода, связанные с миграцией Google Maps.

- [Средства рисования](map-add-drawing-toolbar.md)
- [Ограничить карту двумя пальцами](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Ограничить масштаб колесика прокрутки](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Создание полноэкранного элемента управления](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Службы.**

- [Использование модуля Azure Maps Services](how-to-use-services-module.md)
- [Поиск достопримечательностей](map-search-location.md)
- [Получение информации из координаты (обратный геокод)](map-get-information-from-coordinate.md)
- [Отображение направлений от точки А до точки Б](map-route.md)
- [Поиск автопредложений с помощью пользовательского интерфейса JQuery](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Сопоставление класса веб-пакета SDK с Google Maps v3 с Azure Maps

В следующем приложении представлено сопоставление перекрестных ссылок по наиболее часто используемым классам в Google Maps v3 с их Azure Maps эквивалентами веб-пакета SDK.

### <a name="core-classes"></a>Основные классы

| Карты Google   | Карты Azure  |
|---------------|-------------|
| `google.maps.Map` | [среднеатласский. Таблица](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [среднеатласский. Подсказки](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [среднеатласский. попупоптионс](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [Atlas. Data. позиционировать](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [Atlas. Data. BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [среднеатласский. камераоптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[среднеатласский. камерабаундсоптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[среднеатласский. сервицеоптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[среднеатласский. стилеоптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[среднеатласский. усеринтерактионоптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [среднеатласский. Растров](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Классы наложения

| Карты Google  | Карты Azure  |
|--------------|-------------|
| `google.maps.Marker` | [среднеатласский. хтмлмаркер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[Atlas. Data. Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [среднеатласский. хтмлмаркероптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. Layer. Симболлайер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[среднеатласский. симболлайероптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[среднеатласский. иконоптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[среднеатласский. текстоптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Layer. Бубблелайер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[среднеатласский. бубблелайероптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [Atlas. Data. Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[Atlas. Layer. Полигонлайер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [среднеатласский. полигонлайероптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [Atlas. Layer. Линелайер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [среднеатласский. линелайероптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [Atlas. Data. LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [Atlas. Layer. Линелайер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[среднеатласский. линелайероптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | См. раздел [Добавление круга к карте](map-add-shape.md#add-a-circle-to-the-map) .                                     |
| `google.maps.ImageMapType`  | [среднеатласский. тилелайер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [среднеатласский. тилелайероптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [Atlas. Layer. Имажелайер](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[среднеатласский. имажелайероптионс](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Классы служб

Azure Maps веб-пакет SDK включает [Модуль служб](how-to-use-services-module.md) , который можно загрузить отдельно. Этот модуль служит оболочкой для Azure Maps служб RESTFUL с помощью веб-API и может использоваться в приложениях JavaScript, TypeScript и Node. js.

| Карты Google | Карты Azure  |
|-------------|-------------|
| `google.maps.Geocoder` | [Atlas. Service. Сеарчурл](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [среднеатласский. сеарчаддрессоптионс](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[среднеатласский. сеарчаддрессреврсеоптионс](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[среднеатласский. сеарчаддрессреверсекроссстритоптионс](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[среднеатласский. сеарчаддрессструктуредоптионс](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[среднеатласский. сеарчалонграутеоптионс](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[среднеатласский. сеарчфуззйоптионс](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[среднеатласский. сеарчинсидежеометрйоптионс](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[среднеатласский. сеарчнеарбйоптионс](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[среднеатласский. сеарчпоиоптионс](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[среднеатласский. сеарчпоикатегорйоптионс](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [Atlas. Service. RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [среднеатласский. калкулатераутедиректионсоптионс](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [Atlas. Service. Сеарчурл](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Библиотеки

Библиотеки добавляют дополнительные функции к сопоставлению. Многие из них относятся к основному пакету SDK Azure Maps. Ниже приведены некоторые эквивалентные классы, которые можно использовать вместо этих библиотек Google Maps.

| Карты Google           | Карты Azure   |
|-----------------------|--------------|
| Библиотека рисунков       | [Модуль средств рисования](set-drawing-options.md) |
| Библиотека Geometry      | [Atlas. Math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Библиотека визуализации | [Уровень тепловой карт](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о веб-пакете SDK для Azure Maps.

> [!div class="nextstepaction"]
> [Как использовать библиотеку Map Control в службе "Карты Azure"](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Использование модуля "службы"](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Использование модуля «инструменты рисования»](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Примеры кода](https://docs.microsoft.com/samples/browse/?products=azure-maps)

