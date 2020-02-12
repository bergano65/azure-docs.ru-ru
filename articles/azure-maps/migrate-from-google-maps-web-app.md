---
title: Руководство. Перенос веб-приложения из Google Карт | Microsoft Azure Maps
description: Как перенести веб-приложение из Google Карт в Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: d9b873a058410219bc55abc4f575823b519a646b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989119"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Перенос веб-приложения из Google Карт

Большинство веб-приложений, работающих с Картами Google, используют пакет SDK JavaScript для Google Карт версии 3. Подходящий пакет SDK на основе Azure для переноса — веб-пакет SDK для Azure Maps. Веб-пакет SDK Azure Maps позволяет настраивать интерактивные карты, используя собственные изображения и содержимое. Приложение можно запускать как в веб-, так и в мобильных приложениях. Этот элемент управления использует WebGL, позволяя вам отображать большие наборы данных с высокой производительностью. Вы можете выполнять разработку с помощью пакета SDK, используя JavaScript или TypeScript.

При переносе имеющегося веб-приложения проверьте, использует ли оно библиотеку элементов управления картой с открытым кодом. Примерами такой библиотеки могут быть Cesium, Leaflet или OpenLayers. Если это так и вы не хотите использовать веб-пакет SDK для Azure Maps, еще один вариант переноса приложения — продолжить использовать элемент управления картой с открытым кодом и подключить его к службам фрагментов Azure Maps ([дорожные фрагменты](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [спутниковые фрагменты](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)). Ниже приведены сведения о применении Azure Maps в некоторых часто используемых библиотеках элементов управления картой с открытым кодом.

- Cesium — элемент управления трехмерными картами для Интернета. [Пример кода](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [документация](https://cesiumjs.org/)
- Leaflet — упрощенный элемент управления двухмерными картами для Интернета. [Пример кода](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [документация](https://leafletjs.com/)
- OpenLayers — элемент управления двухмерными картами для Интернета, который поддерживает проекции. [Пример кода](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [документация](https://openlayers.org/)

## <a name="key-features-support"></a>Поддержка основных функций

В следующей таблице перечислены основные функции API, доступные в пакете SDK JavaScript для Google Карт версии 3, и сведения о поддержке функций API в веб-пакете SDK для Azure Maps.

| Функция Google Карт     | Поддержка в веб-пакете SDK для Azure Maps |
|-------------------------|:--------------------------:|
| Метки                 | ✓                          |
| Кластеризация меток       | ✓                          |
| Ломаные линии и многоугольники    | ✓                          |
| Уровни данных             | ✓                          |
| Наземное наложение         | ✓                          |
| Тепловые карты               | ✓                          |
| Слои фрагментов             | ✓                          |
| Слой KML               | ✓                          |
| Инструменты для рисования           | ✓                          |
| Служба геокодирования        | ✓                          |
| Служба направлений      | ✓                          |
| Служба матрицы расстояний | ✓                          |
| Служба высоты       | Запланировано                    |

## <a name="notable-differences-in-the-web-sdks"></a>Важные различия в веб-пакетах SDK

Ниже приведены некоторые ключевые различия между веб-пакетами SDK для Google Карт и Azure Maps, о которых следует знать:

- Помимо предоставления размещенной конечной точки для доступа к веб-пакету SDК Azure Maps, можно также при необходимости использовать пакет NPM для внедрения веб-пакета SDK в приложения. Дополнительные сведения см. в этой [документации](how-to-use-map-control.md). Этот пакет также включает определения TypeScript.
- После создания экземпляра класса Map в Azure Maps код должен ждать запуска для карты события `ready` или `load`, прежде чем взаимодействовать с ней. Этот порядок событий обеспечивает, что все ресурсы карты будут загружены и готовы к доступу.
- Обе платформы используют аналогичную систему фрагментов для базовых карт, однако фрагменты в Google Картах имеют размер 256 пикселей, а фрагменты в Azure Maps — 512. Таким образом, чтобы получить тот же вид карты в Azure Maps, что и в Google Картах, уровень масштабирования, используемый в Google Картах, должен быть вычтен на единицу в Azure Maps.
- В Google Картах координаты используются в формате "широта, долгота", а в Azure Maps — в формате "долгота, широта". Формат Azure Maps соответствует стандартным координатам `[x, y]`, которые используются на большинстве платформ ГИС.
- Фигуры в веб-пакете SDK для Azure Maps основаны на схеме GeoJSON. Вспомогательные классы предоставляются с помощью [пространства имен](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest) *atlas.data*. Существует также класс [*atlas.Shape*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape). Его можно использовать для упаковки объектов GeoJSON, чтобы упростить их обновление и использовать для привязки данных.
- Координаты в Azure Maps определяются как объекты положения. Их можно указать в виде простого массива чисел в формате `[longitude, latitude]` или класса atlas.data.Position(долгота, широта).
    > [!TIP]
    > Класс положения имеет статический вспомогательный метод для импорта координат, которые находятся в формате "широта, долгота". Метод [atlas.data.Position.fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) часто можно заменить методом `new google.maps.LatLng` в коде Google Карт.
- Вместо того чтобы указывать сведения о стилях для каждой фигуры, добавляемой на карту, Azure Maps отделяет стили от данных. Данные хранятся в источниках данных и подключены к слоям подготовки к просмотру. Код Azure Maps использует источники данных для визуализации данных. Такой подход обеспечивает повышение производительности. Кроме того, многие слои поддерживают управляемый данными стиль, где к параметрам стиля слоя можно добавить бизнес-логику. Такая поддержка изменяет способ преобразования отдельных фигур для просмотра в слое на основе свойств, определенных в фигуре.

## <a name="web-sdk-side-by-side-examples"></a>Параллельные примеры веб-пакетов SDK

Ниже приведена коллекция примеров кода для каждой платформы, охватывающих распространенные варианты использования. Они помогут перенести веб-приложение из пакета SDK JavaScript для Google Карт версии 3 в веб-пакет SDK для Azure Maps. Примеры кода, связанные с веб-приложениями, предоставляются на языке JavaScript. Но Azure Maps также предоставляет определения TypeScript в качестве дополнительной возможности с использованием модуля [NPM](how-to-use-map-control.md).

### <a name="load-a-map"></a>Загрузка карты

Загрузка карты в обоих пакетах SDK предполагает один и тот же набор действий.

- Добавьте ссылку на пакет SDK для карт.
- Добавьте в текст страницы тег `div`. Он будет служить заполнителем для карты.
- Создайте функцию JavaScript, которая вызывается после загрузки страницы.
- Создайте экземпляр соответствующего класса Map.

**Некоторые ключевые отличия**

- В Google Картах необходимо указать ключ учетной записи в ссылке на скрипт API. Учетные данные проверки подлинности для Azure Maps указываются в качестве параметров класса Map. Это может быть ключ подписки или сведения Azure Active Directory.
- Google Карты принимают функцию обратного вызова в ссылке на скрипт API, которая используется для вызова функции инициализации для загрузки карты. При работе с Azure Maps следует использовать событие OnLoad страницы.
- При указании ссылки на элемент `div`, в котором будет выполняться визуализация карты, классу `Map` в Azure Maps требуется только значение `id`, тогда как для Google Карт требуется объект `HTMLElement`.
- Координаты в Azure Maps определяются как объекты положения, которые можно указать как простой массив чисел в формате `[longitude, latitude]`.
- Уровень масштаба в Azure Maps на один меньше, чем уровень масштаба в Google Карт. Это несоответствие обусловлено разницей в размерах системы фрагментации между платформами.
- Azure Maps не добавляет на холст карты элементы управления навигацией. Поэтому по умолчанию на карте отсутствуют кнопки масштабирования и кнопки стилей карт. Однако есть элементы управления для добавления средств выбора стиля карт, кнопок масштабирования, компаса или поворота, а также средства управления наклоном.
- Обработчик событий добавляется в Azure Maps для отслеживания события `ready` экземпляра карты. Это событие происходит, когда завершается загрузка контекста WebGL и всех необходимых ресурсов на карте. Добавьте в этот обработчик событий любой код, который требуется выполнить после завершения загрузки карты.

В приведенных ниже примерах показано, как загрузить базовую карту, которая выравнивается по центру Нью-Йорка в точке координат (долгота: −-73,985; широта: 40,747) и находится на уровне масштабирования 12 в Google Картах.

**До: Google Карты**

В следующем примере кода показано, как отобразить Карту Google по центру и увеличить ее расположение.

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

При выполнении этого кода в браузере отобразится показанная ниже карта:

<center>

![Простые Google Карты](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**После: Azure Maps**

В следующем примере кода показано, как загрузить карту с тем же представлением в Azure Maps, а также с элементами управления стилем карт и кнопками масштабирования.

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

При выполнении этого кода в браузере отобразится показанная ниже карта:

<center>

![Простые службы Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

Подробную документацию по настройке и использованию элементов управления картой Azure Maps в веб-приложении см. [здесь](how-to-use-map-control.md).

> [!NOTE]
> В отличие от Google Карт, для Azure Maps не требуется указывать изначальный центр и уровень масштаба при загрузке карты. Если эта информация не будет предоставлена при загрузке карты, карта попытается определить, в каком городе находится пользователь, и будет центрировать и масштабировать карту соответственно.

**Дополнительные ресурсы:**

- Azure Maps также предоставляет элементы управления навигацией для поворота и наклона представления карты, как описано [здесь](map-add-controls.md).

### <a name="localizing-the-map"></a>Локализация карты

Если ваша аудитория распределена по нескольким странам или говорит на разных языках, необходима локализация.

**До: Google Карты**

Чтобы локализовать Google Карты, необходимо добавить параметры языка и региона в скрипт.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Ниже приведен пример использования Google Карт с языком, имеющим значение fr-FR.

<center>

![Локализация Google Карт](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**После: Azure Maps**

Azure Maps предоставляет два разных способа настройки языка и регионального представления карты. Первый вариант — добавить эту информацию в глобальное пространство имен *atlas*, что приведет к тому, что все экземпляры элементов управления картой в вашем приложении будут по умолчанию использовать эти параметры. В приведенном ниже примере кода устанавливается французский язык (fr-FR) и региональное представление со значением auto:

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Второй вариант — передать эту информацию в параметры карты при загрузке карты следующим образом:

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
> В Azure Maps можно загрузить несколько экземпляров карты на одной странице с разными настройками языка и региональными параметрами. Кроме того, можно обновить эти параметры на карте после их загрузки. Подробный список поддерживаемых языков в Azure Maps можно найти [здесь](supported-languages.md).

Ниже приведен пример Azure Maps с установленным языком fr и регионом пользователя fr-FR.

<center>

![Локализация Azure Maps](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>Настройка представления карты

Динамические карты в Azure Maps и Google Картах можно программно перемещать в новые географические расположения. Для этого вызовите соответствующие функции на JavaScript. В примерах ниже показано, как сделать так, чтобы на карте отображались спутниковые аэроснимки, как центрировать карту по расположению и изменить уровень масштабирования на 15 в Google Картах. Используются следующие координаты расположения: долгота — −111,0225 и широта — 35,0272.

> [!NOTE]
> В Google Картах используются фрагменты размером 256 пикселей, а в Azure Maps — более крупные фрагменты размером 512 пикселей. Благодаря этому сокращается количество сетевых запросов, необходимых Azure Maps для загрузки той же области карты, что и в Google Картах. Однако из-за того, как пирамиды фрагментов работают в элементах управления карты, большие фрагменты в Azure Maps означают, что для достижения той же видимой области, что и на карте в Google Картах, при использовании Azure Maps необходимо вычесть единицу от уровня масштабирования, используемого в Google Картах.

**До: Google Карты**

Элемент управления в Google Картах можно переместить программным способом с помощью метода `setOptions`. Этот метод позволяет указать центр карты и уровень масштабирования.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Заданное представление Google Карт](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**После: Azure Maps**

В Azure Maps положение на карте можно изменить программно с помощью метода `setCamera`, а стиль карты — с помощью метода `setStyle`. Координаты в Azure Maps указываются в формате "долгота, широта", а значение уровня масштаба вычитается на единицу.

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

![Заданное представление Azure Maps](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**Дополнительные ресурсы:**

- [Выбор стиля карты](choose-map-style.md)
- [Поддерживаемые стили карт](supported-map-styles.md)

### <a name="adding-a-marker"></a>Добавление метки

В Azure Maps существует несколько способов, с помощью которых данные точек можно визуализировать на карте.

- **Метки HTML** — отрисовывают точки с помощью традиционных элементов DOM. Метки HTML поддерживают перетаскивание.
- **Слой символов** — отрисовывает точки со значками или текстом в контексте WebGL.
- **Пузырьковый слой** — отрисовывает точки в виде окружностей на карте. Радиусы окружностей можно масштабировать на основе свойств данных.

Слои символов и пузырьков отображаются в контексте WebGL. На обоих уровнях можно визуализировать очень большие наборы точек на карте. Данные для этих слоев должны храниться в источнике данных. Источники данных и слои отрисовки необходимо добавлять на карту после запуска события `ready`. Метки HTML отображаются как элементы DOM на странице и не используют источник данных. Чем больше элементов DOM содержит страница, тем медленнее она будет обрабатываться. При отрисовке более чем нескольких сотен точек на карте рекомендуется использовать один из слоев отрисовки.

В следующих примерах на карту в точке координат (долгота:–0,2; широта: 51,5) добавляется метка с наложенным поверх числом 10.

**До: Google Карты**

При использовании Google Карт метки добавляются на карту с помощью класса `google.maps.Marker`, а также путем указания карты в качестве одного из параметров.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![Метка Google Карт](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**После: использование меток HTML в Azure Maps**

В Azure Maps метки HTML можно использовать для отображения точки на карте. Их рекомендовано применять для простых приложений, в которых необходимо показывать небольшое количество точек на карте. Чтобы использовать метку HTML, создайте экземпляр класса `atlas.HtmlMarker`, задайте параметры текста и положения, а затем добавьте на карту метку с помощью метода `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Метка HTML Azure Maps](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**После: использование слоя символов в Azure Maps**

При использовании слоя символов данные необходимо добавить в источник данных, а источник данных — присоединить к слою. Кроме того, источник данных и слой нужно добавить на карту после запуска события `ready`. Чтобы уникальное текстовое значение отображалось над символом, текстовая информация должна храниться как свойство точки данных, а на это свойство должен ссылаться параметр `textField` слоя. Этот способ немного сложнее, чем использование меток HTML, но он значительно повышает производительность.

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

![Слой символов Azure Maps](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**Дополнительные ресурсы:**

- [Создание источника данных](create-data-source-web-sdk.md)
- [Добавление слоя символов](map-add-pin.md)
- [Добавление слоя пузырьков](map-add-bubble-layer.md)
- [Данные точек кластера](clustering-point-data-web-sdk.md)
- [Добавление меток HTML](map-add-custom-html.md)
- [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)
- [Параметры значков слоя символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Параметры текста слоя символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Класс метки HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Параметры метки HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Добавление настраиваемой метки

Для представления точек на карте можно использовать пользовательские изображения. На изображении карты ниже используется настраиваемое изображение для отображения точки на карте. Координаты точки: широта — 51,5, долгота — −0,2. Привязка смещает положение маркера таким образом, чтобы точка значка вешки была правильно размещена на карте.

<center>

![Изображение желтой вешки](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

**До: Google Карты**

В Google Картах пользовательская метка создается путем указания объекта `Icon`, содержащего `url` для изображения, и точки `anchor` для выравнивания точки изображения вешки в соответствии с координатой на карте. Значение привязки в Google Картах связано относительно левого верхнего угла изображения.

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

![Пользовательская метка Google Карт](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**После: использование меток HTML в Azure Maps**

Чтобы настроить метку HTML в Azure Maps, можно передать в параметр `htmlContent` метки объект HTML `string` или `HTMLElement`. В Azure Maps параметр `anchor` позволяет указать положение метки относительно координаты расположения, используя одну из девяти определенных опорных точек: "центр", "внизу", "слева", "справа", "вверху слева", "вверху справа", "внизу слева", "внизу справа". Содержимое привязывается к нижнему центру содержимого HTML по умолчанию. Чтобы упростить перенос кода из Google Карт, установите для `anchor` значение top-left (вверху слева), а затем используйте параметр `pixelOffset` с тем же смещением, которое использовалось в Google Картах. Смещение в Azure Maps выполняется в противоположном направлении по отношению к Google Картам, поэтому умножьте его на минус единицу.

> [!TIP]
> Добавьте `pointer-events:none` в качестве стиля в HTML-содержимое, чтобы отключить поведение перетаскивания по умолчанию в Microsoft Edge, при котором будет отображаться нежелательный значок.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Пользовательская метка HTML в Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**После: использование слоя символов в Azure Maps**

Слои символов в Azure Maps также поддерживают пользовательские изображения, но сначала необходимо загрузить изображение в ресурсы карты и назначить ему уникальный идентификатор. Затем слой символов сможет ссылаться на этот идентификатор. Символ может быть смещен для выравнивания в соответствии с нужной точкой на изображении с помощью значка `offset`. В Azure Maps параметр `anchor` позволяет указать положение символа относительно координаты расположения с использованием одной из девяти определенных опорных точек: "центр", "внизу", "слева", "справа", "вверху слева", "вверху справа", "внизу слева", "внизу справа". Содержимое привязывается к нижнему центру содержимого HTML по умолчанию. Чтобы упростить перенос кода из Google Карт, установите для `anchor` значение top-left (вверху слева), а затем используйте параметр `offset` с тем же смещением, которое использовалось в Google Картах. Смещение в Azure Maps выполняется в противоположном направлении по отношению к Google Картам, поэтому умножьте его на минус единицу.

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

![Пользовательский слой значков Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> Чтобы создать расширенную пользовательскую отрисовку точек, используйте одновременно несколько слоев отрисовки. Например, если требуется использовать несколько вешек с одинаковым значком на разных цветных окружностях. Вместо того чтобы создавать множество изображений для каждого цвета, наложите слой символов поверх слоя пузырьков таким образом, чтобы эти слои ссылались на один и тот же источник данных. Это будет намного эффективнее, чем создавать и сохранять на карте множество различных изображений.

**Дополнительные ресурсы:**

- [Создание источника данных](create-data-source-web-sdk.md)
- [Добавление слоя символов](map-add-pin.md)
- [Добавление меток HTML](map-add-custom-html.md)
- [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)
- [Параметры значков слоя символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Параметры текста слоя символов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Класс метки HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Параметры метки HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Добавление ломаной линии

Ломаные линии используются для представления линий или траекторий на карте. В следующих примерах показано, как создать пунктирную ломаную линию на карте.

**До: Google Карты**

В Google Картах класс Polyline принимает набор параметров. Массив координат передается в параметре `path` ломаной линии.

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

![Ломаная линия в Google Картах](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**После: Azure Maps**

В Azure Maps ломаные линии называются объектами `LineString` или `MultiLineString`. Эти объекты можно добавлять в источник данных и отображать с помощью слоя линий.

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

![Ломаная линия в Azure Maps](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**Дополнительные ресурсы:**

- [Добавление линий на карту](map-add-line-layer.md)
- [Параметры слоя линий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Добавление многоугольника

Многоугольники используются для представления области на карте. Azure Maps и Google Карты обеспечивают похожую поддержку многоугольников. В следующих примерах показано, как создать многоугольник, который образует треугольник на основе центральной координаты на карте.

**До: Google Карты**

В Google Картах класс Polygon принимает набор параметров. Массив координат передается в параметре `paths` многоугольника.

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

![Многоугольник в Google Картах](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**После: Azure Maps**

В Azure Maps объекты `Polygon` и `MultiPolygon` можно добавить в источник данных и преобразовать для просмотра на карте с помощью слоев. Площадь многоугольника можно отобразить на слое многоугольников. Контур многоугольника можно визуализировать с помощью слоя линий.

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

![Многоугольник в Azure Maps](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**Дополнительные ресурсы:**

- [Добавление многоугольника на карту](map-add-shape.md)
- [Добавление окружности на карту](map-add-shape.md#add-a-circle-to-the-map)
- [Параметры слоя многоугольников](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Параметры слоя линий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Отображение окна сведений

Дополнительные сведения для сущности можно показать на карте, используя класс `google.maps.InfoWindow` в Google Картах. В Azure Maps это можно сделать с помощью класса `atlas.Popup`. В следующих примерах на карту добавляется метка, при щелчке которой отображается обычное или всплывающее окно сведений.

**До: Google Карты**

При использовании Google Карт окно сведений создается с помощью конструктора `google.maps.InfoWindow`.

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

![Всплывающее окно в Google Картах](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**После: Azure Maps**

В Azure Maps во всплывающем окне можно вывести дополнительные сведения о расположении. Объект HTML `string` или `HTMLElement` можно передать в параметр `content` всплывающего окна. При необходимости всплывающие окна можно показывать независимо от любой фигуры. Поэтому для всплывающих окон требуется указать значение `position`. Чтобы отобразить всплывающее окно, вызовите метод `open` и передайте карту `map`, в которой должно отображаться всплывающее окно.

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

![Всплывающее окно в Azure Maps](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> Чтобы сделать то же самое со слоем символов, пузырьков, линией или слоем многоугольников, просто передайте слой в код события карт вместо метки.

**Дополнительные ресурсы:**

- [Добавление всплывающего окна](map-add-popup.md)
- [Всплывающее окно с мультимедийным содержимым](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Всплывающие окна на фигурах](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Повторное использование всплывающего окна с несколькими точками](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Класс Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Параметры всплывающего окна](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>Импорт файла GeoJSON

Google Карты поддерживают загрузку и динамическую стилизацию данных GeoJSON с помощью класса `google.maps.Data`. Функциональность этого класса в значительной степени совпадает с основанной на данных стилизацией Azure Maps. Одно из ключевых отличий заключается в том, что в Google Картах вы указываете функцию обратного вызова и бизнес-логику для стилизации каждого компонента, отдельно обрабатываемого в потоке пользовательского интерфейса. В Azure Maps слои поддерживают указание выражений, управляемых данными, в виде параметров стилизации. Эти выражения обрабатываются во время преобразования для просмотра в отдельном потоке. Это увеличивает производительность отрисовки, а также позволяет быстрее визуализировать большие наборы данных.

В следующих примерах загружается канал GeoJSON с данными обо всех землетрясениях за последние семь дней, сообщенными Геологической службой США. Они отображаются в виде масштабируемых окружностей на карте. Цвет и масштаб каждой окружности зависят от магнитуды каждого землетрясения, которая хранится в свойстве `"mag"` каждого компонента в наборе данных. Если магнитуда больше или равна пяти, окружность будет красной. Если больше или равна трем, но меньше пяти, окружность будет оранжевой. Если она меньше трех, окружность будет зеленой. Радиус каждой окружности будет экспонентой магнитуды, умноженной на 0,1.

**До: Google Карты**

В Google Картах в методе `map.data.setStyle` можно указать единую функцию обратного вызова. Этот метод будет использоваться для применения бизнес-логики к каждому компоненту, загруженному из канала GeoJSON с помощью метода `map.data.loadGeoJson`.

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

![GeoJSON в Google Картах](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**После: Azure Maps**

GeoJSON является базовым типом данных в Azure Maps и может легко импортироваться в источник данных с помощью метода `datasource.importFromUrl`. Слой пузырьков предоставляет функции для отрисовки масштабируемых окружностей на основе свойств компонентов в источнике данных. Вместо функции обратного вызова бизнес-логика преобразуется в выражение и передается в параметры стиля. Выражения определяют, как работает бизнес-логика. Выражения можно передавать в другой поток и вычислять по данным компонентов. В Azure Maps можно добавлять несколько источников данных и слоев, у каждого из которых разная бизнес-логика. Это позволяет показывать несколько наборов данных на карте разными способами.

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

![GeoJSON в Azure Maps](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**Дополнительные ресурсы:**

- [Добавление слоя символов](map-add-pin.md)
- [Добавление слоя пузырьков](map-add-bubble-layer.md)
- [Данные точек кластера](clustering-point-data-web-sdk.md)
- [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Кластеризация меток

При визуализации большого количества точек данных на карте точки перекрываются, а сама карта выглядит перегруженной, из-за чего ее будет сложно просматривать и использовать. С помощью кластеризации точек данных можно улучшить удобство работы пользователей, а также повысить производительность. Кластеризация данных точек — это процесс объединения данных точек, находящихся рядом друг с другом, и представления их на карте в виде единой кластеризованной точки данных. По мере того как пользователь масштабирует карту, кластеры разбиваются на отдельные точки данных.

В следующих примерах код загружает канал GeoJSON с данными о землетрясениях за прошлую неделю, после чего он добавляется на карту. Кластеры отрисовываются как масштабируемые и цветные окружности в зависимости от количества содержащихся в них точек.

> [!NOTE]
> Существует несколько различных алгоритмов кластеризации меток. Google Карты и Azure Maps используют несколько отличные алгоритмы. Поэтому распределение точек в кластерах иногда может отличаться.

**До: Google Карты**

В Google Картах можно выполнять кластеризацию меток путем загрузки данных в библиотеку MarkerClusterer. Значки кластеров представляют собой изображения с числами от одного до пяти в качестве имен и размещаются в одном каталоге.

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

![Кластеризация в Google Картах](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**После: Azure Maps**

В Azure Maps добавление данных и управление ими осуществляется с помощью источника данных. Слои подключаются к источникам данных и визуализируют данные в них. Класс `DataSource` в Azure Maps предоставляет несколько параметров кластеризации.

- `cluster` — указывает источнику данных выполнить кластеризацию данных точек.
- `clusterRadius` — радиус в пикселях, используемый для кластеризации точек.
- `clusterMaxZoom` — максимальный уровень масштаба, в котором происходит кластеризация. При дополнительном увеличении все точки отрисовываются как символы.
- `clusterProperties` — определяет пользовательские свойства, которые рассчитываются с помощью выражений для всех точек в каждом кластере и добавляются в свойства каждой точки кластера.

Если кластеризация включена, источник данных будет отсылать кластеризованные и некластеризованные точки данных слоям для отрисовки. Источник данных способен выполнять кластеризацию сотен тысяч точек данных. Кластеризованная точка данных имеет следующие свойства.

| Имя свойства             | Тип    | Описание   |
|---------------------------|---------|---------------|
| `cluster`                 | Логическое | Указывает, представляет ли компонент кластер. |
| `cluster_id`              | строка  | Уникальный идентификатор кластера, который можно использовать с методами `getClusterExpansionZoom`, `getClusterChildren` и `getClusterLeaves` для DataSource. |
| `point_count`             | number  | Число точек, содержащихся в кластере.  |
| `point_count_abbreviated` | строка  | Строка, которая сокращает значение `point_count`, если оно длинное (например, 4000 преобразуется в 4K).  |

Класс `DataSource` имеет следующую вспомогательную функцию для доступа к дополнительным сведениям о кластере с помощью `cluster_id`.

| Метод | Возвращаемый тип | Описание |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Promise&lt;Массив&lt;Компонент&lt;Геометрия, любая&gt; \| Фигура&gt;&gt; | Извлекает дочерние элементы заданного кластера на следующем уровне масштабирования. Эти дочерние элементы могут быть сочетанием фигур и подкластеров. Подкластеры будут представлять собой компоненты со свойствами, соответствующими ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Promise&lt;число&gt; | Вычисляет уровень масштабирования, при котором кластер начинает расширяться или разбиваться на части. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Promise&lt;Массив&lt;Компонент&lt;Геометрия, любая&gt; \| Фигура&gt;&gt; | Извлекает все точки в кластере. Задайте `limit`, чтобы вернуть подмножество точек, и используйте `offset`, чтобы пролистать страницы с точками. |

При отрисовке кластеризованных данных на карте зачастую проще использовать два или более слоев. В следующем примере используются три слоя. Слой пузырьков для визуализации масштабируемых цветных окружностей на основе размеров кластеров. Слой символов для отрисовки размера кластера в виде текста и второй слой символов для визуализации некластеризованных точек. Существует много других способов отображения кластеризованных данных в Azure Maps. Дополнительные сведения см. в статье [Clustering point data](clustering-point-data-web-sdk.md) (Данные точки кластеризации).

Данные GeoJSON можно импортировать непосредственно в Azure Maps с помощью функции `importDataFromUrl` в классе `DataSource`.

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

![Кластеризация в Azure Maps](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**Дополнительные ресурсы:**

- [Добавление слоя символов](map-add-pin.md)
- [Добавление слоя пузырьков](map-add-bubble-layer.md)
- [Данные точек кластера](clustering-point-data-web-sdk.md)
- [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Добавление тепловой карты

Тепловая карта, которую также называют картой точек плотности, представляет собой тип визуализации данных. Они используются для визуализации плотности данных с помощью диапазона цветов. Такие визуализации часто используются для отображения "горячих точек" данных на карте. Тепловые карты особенно полезны для визуализации больших наборов данных точек.

В следующих примерах загружается канал GeoJSON с данными обо всех землетрясениях за прошлый месяц из Геологической службы США, которые отображаются в виде взвешенной тепловой карты, где в качестве веса используется свойство `"mag"`.

**До: Google Карты**

Чтобы создать тепловую карту в Google Картах, необходимо загрузить библиотеку визуализаций, добавив `&libraries=visualization` в URL-адрес скрипта API. Слой тепловой карты в Google Картах не поддерживает прямое преобразование данных в формате GeoJSON. Вместо этого данные сначала необходимо скачать и преобразовать в массив взвешенных точек данных.

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

![Тепловая карта в Google Картах](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**После: Azure Maps**

В Azure Maps загрузите данные GeoJSON в источник данных и подключите источник к слою тепловой карты. Свойство, которое будет использоваться для веса, можно передать в параметр `weight` с помощью выражения. Данные GeoJSON можно импортировать непосредственно в Azure Maps с помощью функции `importDataFromUrl` в классе `DataSource`.

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

![Тепловая карта в Azure Maps](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**Дополнительные ресурсы:**

- [Добавление слоя тепловой карты](map-add-heat-map-layer.md)
- [Класс слоя тепловой карты](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Параметры слоя тепловой карты](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Использование стилистических выражений на основе данных](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Наложение слоя фрагментов

Слои фрагментов также известны как наложения изображений в Google Картах. Они позволяют накладывать большие изображения, разбитые на небольшие фрагменты, которые соответствуют разбиению карты. Такой подход часто используется для наложения больших изображений или больших наборов данных.

В приведенных ниже примерах накладывается слой фрагментов радара погоды из лаборатории окружающей среды Университета штата Айова.

**До: Google Карты**

В Google Картах слои фрагментов можно создавать с помощью класса `google.maps.ImageMapType`.

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

![Слой фрагментов в Google Картах](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**После: Azure Maps**

В Azure Maps слой фрагментов можно добавить на карту во многом так же, как и любой другой слой. Форматированный URL-адрес с заполнителями значений x, y, масштаба (`{x}`, `{y}`, `{z}` соответственно) используется для указания уровня доступа к фрагментам. Слои фрагментов в Azure Maps также поддерживают заполнители `{quadkey}`, `{bbox-epsg-3857}` и `{subdomain}`.

> [!TIP]
> В Azure Maps слои можно легко отобразить под другими слоями, в том числе под базовыми слоями карт. Часто желательно отображать слои фрагментов под метками карты, чтобы их можно было легко читать. Метод `map.layers.add` принимает второй параметр — идентификатор слоя, в который следует вставить новый слой. Чтобы вставить слой фрагментов под метками карты, можно использовать следующий код: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Слой фрагментов в Azure Maps](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> Запросы на фрагмент можно записать с помощью параметра `transformRequest` карты. Это позволит при необходимости изменить или добавить заголовки в запрос.

**Дополнительные ресурсы:**

- [Добавление слоев фрагмента карты](map-add-tile-layer.md)
- [Класс слоя фрагментов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Параметры слоя фрагментов](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Отображение данных дорожного движения

Данные дорожного движения можно перенести на карту как в Azure Maps, так и в Google Картах.

**До: Google Карты**

Данные дорожного движения в Google Картах можно наложить на карту, используя слой дорожного движения.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Данные дорожного движения в Google Картах](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**После: Azure Maps**

Azure Maps предоставляет несколько различных вариантов отображения дорожного движения. Дорожно-транспортные происшествия, такие как закрытие дорог и аварии, можно отображать на карте в виде значков. На карте можно наложить транспортный поток с использованием дорог с цветовой кодировкой, а цвета можно изменять в соответствии с ограничениями скорости, относительно нормальной ожидаемой или абсолютной задержки. Данные о дорожно-транспортных происшествиях в Azure Maps обновляются каждую минуту, а данные о дорожном движении — каждые две минуты.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Данные дорожного движения в Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Если щелкнуть один из значков дорожного движения в Azure Maps, во всплывающем окне отобразятся дополнительные сведения.

<center>

![Дорожно-транспортные происшествия в Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**Дополнительные ресурсы:**

- [Отображение данных дорожного движения на карте](map-show-traffic.md)
- [Параметры наложения сведений о дорожном движении](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Добавление наземного наложения

Карты Azure и Google поддерживают наложение изображений с геоссылками на карте таким образом, что они перемещаются и масштабируются по мере сдвига и масштабирования карты. В Google Картах они называются наземными наложениями, а в Azure Maps — слоями изображений. Они отлично подходят для создания планов этажей, наложения старых карт или изображений, снятых с помощью дронов.

**До: Google Карты**

При создании наземного наложения в Google Картах необходимо указать URL-адрес изображения, которое необходимо наложить, и ограничивающий прямоугольник для выделения изображения на карте. В этом примере показано, как наложить на карту изображение [карты Ньюарка, Нью-Джерси, (1922 г.)](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg).

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

При выполнении этого кода в браузере отобразится показанная ниже карта:

<center>

![Наложение изображений в Google Картах](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**После: Azure Maps**

В Azure Maps изображения с геоссылками можно накладывать с помощью класса `atlas.layer.ImageLayer`. Этому классу требуется URL-адрес изображения и набор координат для четырех углов изображения. Изображение должно размещаться либо в том же домене, либо поддерживать CORS.

> [!TIP]
> Если у вас есть только информация о координатах севера, юга, востока, запада и повороте, а не координаты каждого угла изображения, можно использовать статический метод [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-).

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

![Наложение изображений в Azure Maps](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**Дополнительные ресурсы:**

- [Наложение изображения](map-add-image-layer.md)
- [Класс слоя изображений](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>Дополнительные примеры кода

Ниже приведены некоторые дополнительные примеры кода, связанные с перемещением из Google Карт.

- [Инструменты для рисования](map-add-drawing-toolbar.md)
- [Ограничение карты до масштабирования с помощью двух пальцев](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Ограничение карты до масштабирования с помощью колесика прокрутки](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Создание полноэкранного элемента управления](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Службы.**

- [Использование модуля служб Azure Maps](how-to-use-services-module.md)
- [Поиск достопримечательностей](map-search-location.md)
- [Получение информации из координаты (обратный геокод)](map-get-information-from-coordinate.md)
- [Отображение направлений от точки А до точки Б](map-route.md)
- [Автозаполнение в поисковых запросах с использованием пользовательского интерфейса JQuery](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Сопоставление классов пакета SDK для Google Карт версии 3 с классами веб-пакета SDK для Azure Maps

В следующем приложении приводится перекрестное сопоставление наиболее часто используемых классов в пакете SDK для Google Карт версии 3 с их эквивалентами в веб-пакете SDK для Azure Maps.

### <a name="core-classes"></a>Основные классы

| Google Карты   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [atlas.Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [atlas.Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [atlas.PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Position](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [atlas.data.BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [atlas.CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[atlas.CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[atlas.ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[atlas.StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[atlas.UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [atlas.Pixel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Классы наложения

| Google Карты  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [atlas.HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[atlas.data.Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [atlas.HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[atlas.SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[atlas.IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[atlas.TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[atlas.BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [atlas.data.Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [atlas.PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [atlas.data.LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | См. сведения в раздела о [добавлении окружности на карту](map-add-shape.md#add-a-circle-to-the-map).                                     |
| `google.maps.ImageMapType`  | [atlas.TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [atlas.TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[atlas.ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Классы служб

Веб-пакет SDK для Azure Maps содержит модуль служб, который можно загрузить отдельно. Этот модуль создает оболочку для служб REST Azure Maps с веб-API и может использоваться в приложениях JavaScript, TypeScript и Node.js.

| Google Карты | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [atlas.SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [atlas.CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Библиотеки

Библиотеки добавляют дополнительные функции к картам. Многие из этих функций содержатся в основном пакете SDK для Azure Maps. Ниже приведены некоторые эквивалентные классы, которые можно использовать вместо этих библиотек Google Карт.

| Google Карты           | Azure Maps   |
|-----------------------|--------------|
| Библиотека средств рисования       | [Модуль средств рисования](set-drawing-options.md) |
| Библиотека геометрических фигур      | [atlas.math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Библиотека визуализаций | [Слой тепловой карты](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с дополнительными сведениями о веб-пакете SDK для Azure Maps.

> [!div class="nextstepaction"]
> [Как использовать библиотеку Map Control в службе "Карты Azure"](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Использование модуля служб Azure Maps](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Использование модуля средств рисования](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Примеры кода](https://docs.microsoft.com/samples/browse/?products=azure-maps)

