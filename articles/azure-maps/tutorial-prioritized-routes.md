---
title: Использование нескольких маршрутов с помощью службы "Карты Azure" | Документация Майкрософт
description: Поиск маршрутов для различных способов перемещения с помощью службы "Карты Azure"
author: dsk-2015
ms.author: dkshir
ms.date: 10/02/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 340bf83f07b9e730cc43baccc60a39f5ba1f9942
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815313"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Поиск маршрутов для различных способов перемещения с помощью службы "Карты Azure"

В этом руководстве показано, как использовать учетную запись службы "Карты Azure" и службу построения маршрутов, чтобы найти маршрут к объекту с учетом приоритета способа перемещения. На карте отображается два разных маршрута: один для легковых автомобилей, а другой для грузовиков, маршрут которых может ограничиваться из-за высоты, веса или опасного груза. Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание веб-страницы с помощью API элементов управления картой;
> * Визуализация потока трафика на карте.
> * Создание запросов маршрута по видам транспорта.
> * Отображение нескольких маршрутов на карте.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем продолжить, выполните действия из первого руководства, чтобы [создать учетную запись службы "Карты Azure"](./tutorial-search-location.md#createaccount) и [получить для нее ключ подписки](./tutorial-search-location.md#getkey).

## <a name="create-a-new-map"></a>Создание карты

Чтобы создать статическую HTML-страницу со встроенным API элементов управления картой, сделайте следующее.

1. На локальном компьютере создайте файл **MapTruckRoute.html**.
2. Добавьте в него следующие компоненты HTML.

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.min.js?api-version=1"></script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>

    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    Заголовок HTML внедряет расположения ресурсов для CSS-файлов и файлов JavaScript из библиотеки службы "Карты Azure". Сегмент *script* в тексте HTML-файла будет содержать встроенный код JavaScript для карты.
3. Добавьте следующий код JavaScript в блок *script* HTML-файла. Замените строку **\<your account key\>** первичным ключом, скопированным из учетной записи службы "Карты Azure". Если вы не укажите на карте конкретный маршрут, отобразится представление целого мира. Этот код по умолчанию задает центральную точку для карты и объявляет уровень масштаба, чтобы вы имели возможность сосредоточиться на конкретной области.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
         center: [-118.2437, 34.0522],
         zoom: 12
    });
    ```
    **atlas.Map** предоставляет элемент управления для визуальной интерактивной веб-карты и является компонентом API Azure Map Control.

4. Сохраните файл и откройте его в браузере. На этом этапе у вас есть базовая карта, которую можно будет дополнительно доработать. 

   ![Просмотр базовой карты](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Визуализация потока трафика

1. Добавьте отображение потока трафика на карту.  **map.addEventListener** гарантирует, что все функции карты добавляются после полной загрузки карты.

    ```JavaScript
    map.addEventListener("load", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```
    Этот код задает для потока трафика значение `relative`. Это скорость на дороге относительно безостановочного потока. Вы также можете задать скорость `absolute` или `relative-delay`, тогда будут отображаться отличия относительной скорости от безостановочного потока.

2. Сохраните файл **MapTruckRoute.html** и обновите страницу браузера. Должны отобразиться улицы Лос-Анджелеса с текущими данными трафика.

   ![Просмотр карты с трафиком](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="set-start-and-end-points"></a>Установка начальных и конечных точек

В этом руководстве задайте в качестве начальной точки вымышленную компанию Fabrikam в Сиэтле, а в качестве пункта назначения — офис корпорации Майкрософт.

1. Добавьте следующий код JavaScript для создания меток начальной и конечной точек маршрута.

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Fabrikam, Inc.",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Microsoft",
        icon: "pin-blue"
    });
    ```
    Этот код создает два [объекта GeoJSON](https://en.wikipedia.org/wiki/GeoJSON), представляющих начальную и конечную точки маршрута.

2. Добавьте следующий код JavaScript для добавления на карту начальной и конечной точек.

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 100
    });
    
    map.addEventListener("load", function() { 
        // Add pins to the map for the start and end point of the route
        map.addPins([startPin, destinationPin], {
            name: "route-pins",
            textFont: "SegoeUi-Regular",
            textOffset: [0, -20]
        });
    });
    ```
    Вызов **map.setCameraBounds** корректирует окно карты в соответствии с координатами начальной и конечной точек. **map.addEventListener** гарантирует, что все функции карты добавляются после полной загрузки карты. **map.addPins** API добавляет точки в Map Control в виде визуальных компонентов.

3. Сохраните файл и обновите браузер, чтобы на карте отобразились пометки. Несмотря на то, что вы определили на карте центральную точку в Лос-Анджелесе, **map.setCameraBounds** передвинуло представление, чтобы отобразить начальную и конечную точку.

   ![Просмотр карт с начальной и конечной точкой](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Построение маршрутов учетом приоритета способа перемещения

В этом разделе показано, как использовать API службы построения маршрутов "Карты" для поиска нескольких маршрутов из заданной начальной точки к точке назначения в соответствии со способом перемещения. Служба построения маршрутов предоставляет интерфейсы API для планирования самого *быстрого*, *краткого*, *экономичного* или *захватывающего* маршрута между двумя расположениями с текущими условиями трафика. Она также позволяет пользователям планировать маршруты в будущем с помощью обширной базы данных Azure, содержащей исторический трафик, и прогнозирования длительности маршрутов в любой день и любое время. Дополнительные сведения см. в статье [Route — Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Маршрут. Получение направления маршрута).  Все приведенные ниже блоки кода необходимо добавить в **блок загрузки карты eventListener**. Это позволит гарантировать их загрузку после полной загрузки карты.

1. Во-первых, добавьте новый слой на карту, чтобы отобразить путь маршрута (или *linestring*). В этом руководстве приведено два разных маршрута **car-route** и **truck-route**, каждый из которых имеет собственный стиль. Добавьте следующий код JavaScript в блок *script*.

    ```JavaScript
    // Place route layers on the map
    var carRouteLayerName = "car-route";
    map.addLinestrings([], {
        name: carRouteLayerName,
        color: "#B76DAB",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });

    var truckRouteLayerName = "truck-route";
    map.addLinestrings([], {
        name: truckRouteLayerName,
        color: "#2272B9",
        width: 9,
        cap: "round",
        join: "round",
        before: carRouteLayerName
    });
    ```

2. Добавьте следующий код JavaScript в блок *script*, чтобы запросить маршрут для грузовика и отобразить результаты на карте.

    ```JavaScript
    // Instantiate the service client  
    var client = new atlas.service.Client(MapsAccountKey);

    // Construct the route query string
    var routeQuery = startPoint.coordinates[1] +
        "," +
        startPoint.coordinates[0] +
        ":" +
        destinationPoint.coordinates[1] +
        "," +
        destinationPoint.coordinates[0];

    // Execute the truck route query then add the route to the map once a response is received  
    client.route.getRouteDirections(routeQuery, {
        travelMode: "truck",
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: "USHazmatClass2"
    }).then(response => {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson
            .GeoJsonRouteDirectionsResponse(response);

        // Get the first in the array of routes and add it to the map
        map.addLinestrings([geoJsonResponse.getGeoJsonRoutes().features[0]], {
            name: truckRouteLayerName
        });
    });
    ```
    Приведенный выше фрагмент кода создает клиент службы и строку запроса маршрута. Затем с помощью метода [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) создается запрос к службе построения маршрутов Azure Maps, а затем с помощью метода [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest) анализируется ответ в формате GeoJSON. Затем он создает массив координат для полученного маршрута и добавляет его на слой карты `truckRouteLayerName`.

3. Добавьте следующий код JavaScript, чтобы запросить маршрут для легкового автомобиля и отобразить результаты.

    ```JavaScript
    // Execute the car route query then add the route to the map once a response is received  
    client.route.getRouteDirections(routeQuery).then(response => {
        // Parse the response into GeoJSON
        var geoJsonResponse = new tlas.service.geojson
            .GeoJsonRouteDiraectionsResponse(response);

        // Get the first in the array of routes and add it to the map 
        map.addLinestrings([geoJsonResponse.getGeoJsonRoutes().features[0]], {
            name: carRouteLayerName
        });
    });
    ```
    В этом фрагменте кода для автомобиля используется тот же запрос маршрута грузовика. С помощью метода [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) создается запрос к службе построения маршрутов Azure Maps, а затем с помощью метода [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest) анализируется ответ в формате GeoJSON. Затем он создает массив координат для полученного маршрута и добавляет его на слой карты `carRouteLayerName`.

4. Сохраните файл **MapTruckRoute.html** и обновите браузер, чтобы просмотреть результат. Для успешного подключения к интерфейсам API службы "Карты Azure" должна использоваться карта следующего вида.

    ![Поиск маршрутов с учетом приоритета с помощью службы построения маршрутов Azure](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Линия маршрута грузовика голубая и более толстая, а линия маршрута легкового автомобиля сиреневая и более тонкая. Маршрут легкового автомобиля проходит через туннели на озере Вашингтон (федеральная автострада номер 90), которые расположены в жилых районах. Поэтому здесь запрещается перевозить опасные отходы. Маршрут грузовика, указывающий тип груза USHazmatClass2, использует другую магистраль.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * создание веб-страницы с помощью API элементов управления картой;
> * Визуализация потока трафика на карте.
> * Создание запросов маршрута по видам транспорта.
> * Отображение нескольких маршрутов на карте.

Пример кода для этого руководства приведен здесь:

> [Определение нескольких маршрутов с помощью Azure Maps](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/truckRoute.html)

Дополнительные сведения о времени сервисного обслуживания и возможностях службы Azure Maps см. здесь:

> [!div class="nextstepaction"]
> [Уровни увеличения и параметры сетки](zoom-levels-and-tile-grid.md)

Дополнительные примеры кода и сведения о возможностях интерактивного программирования см. здесь:

> [!div class="nextstepaction"]
> [Как использовать библиотеку Map Control в службе "Карты Azure"](how-to-use-map-control.md)