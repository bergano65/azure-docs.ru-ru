---
title: Использование нескольких маршрутов с помощью службы "Карты Azure" | Документация Майкрософт
description: Поиск маршрутов для различных способов перемещения с помощью службы "Карты Azure"
author: walsehgal
ms.author: v-musehg
ms.date: 11/14/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 0ec047f38596bed4d3f0bc5520dc9c7fc18b4c24
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56585242"
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
    <html>
    <head>
        <title>Multiple routes by mode of travel</title>
        
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                position: relative;
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```
    
    Обратите внимание на то, что заголовок HTML содержит файлы ресурсов CSS и JavaScript, размещенные в библиотеке Azure Map Control. Обратите внимание на событие `onload` в тексте страницы, которое вызовет функцию `GetMap` после загрузки текста страницы. Эта функция будет содержать внутренний код JavaScript для доступа к интерфейсам службы Azure Maps.

3. Добавьте следующий код JavaScript в функцию `GetMap`. Замените строку **\<Your Azure Maps Key\>** первичным ключом, скопированным из учетной записи службы Maps.

    ```JavaScript
    //Add your Azure Maps subscription key to the map SDK. 
    atlas.setSubscriptionKey('<Your Azure Maps Key>');

    //Initialize a map instance.
    map = new atlas.Map('myMap');
    ```

    **atlas.Map** предоставляет элемент управления для визуальной интерактивной веб-карты и является компонентом API Azure Map Control.

4. Сохраните файл и откройте его в браузере. На этом этапе у вас есть базовая карта, которую можно будет дополнительно доработать.

   ![Просмотр базовой карты](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Визуализация потока трафика

1. Добавьте отображение потока трафика на карту. `map.events.add` гарантирует, что все функции карты добавятся после полной загрузки карты.

    ```JavaScript
    map.events.add("load", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```
    
     На карту добавляется событие загрузки, которое сработает, когда ресурсы карты будут полностью загружены. В обработчике событий загрузки карты для параметра потока трафика на карте установлено значение `relative`, что является скоростью потока трафика относительно свободного потока. Вы также можете задать скорость `absolute` или `relative-delay`, тогда будут отображаться отличия относительной скорости от безостановочного потока.

2. Сохраните файл **MapTruckRoute.html** и обновите страницу браузера. Должны отобразиться улицы Лос-Анджелеса с текущими данными трафика.

   ![Просмотр карты с трафиком](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Определение способа отображения маршрута

В этом руководстве будут вычислены и отображены на карте два маршрута. Один маршрут проходит по дорогам, предназначенным для легковых автомобилей, а другой — для грузовиков. При визуализации мы будем использовать значок символа для обозначения начальной и конечной точек и линии разного цвета для обозначения каждого пути маршрута.

1. В функции GetMap после инициализации карты добавьте следующий код JavaScript.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round',
            filter: ['==', '$type', 'LineString']
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
            },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['==', '$type', 'Point']
        }));
    });
    ```
    
    На карту добавляется событие загрузки, которое сработает, когда ресурсы карты будут полностью загружены. В обработчике событий загрузки карты создается источник данных для хранения линий маршрута, а также начальной и конечной точек. Слой линий создается и привязывается к источнику данных, чтобы определить, как будет отображаться линия маршрута. Выражения используются для извлечения ширины и цвета линии из свойств функции линии маршрута. Чтобы на этом слое отображались только данные GeoJSON LineString, добавляется фильтр. При добавлении слоя карты передается второй параметр со значением `'labels'`, в котором указывается, что этот слой будет отображаться под метками карты. Это гарантирует, что линия маршрута не будет закрывать дорожные метки. Слой символов создается и привязывается к источнику данных. Этот слой определяет, как будут отображаться начальная и конечная точки. В этом случае были добавлены выражения для извлечения изображения значка и текстовых подписей из свойств в каждом объекте точки. 
    
2. В этом руководстве задайте в качестве начальной точки вымышленную компанию Fabrikam в Сиэтле, а в качестве пункта назначения — офис корпорации Майкрософт. В обработчике событий загрузки карты добавьте следующий код.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-blue'
    });
    ```
    
    Этот код создает два [объекта GeoJSON](https://en.wikipedia.org/wiki/GeoJSON), представляющих начальную и конечную точки маршрута. В каждую точку добавляется свойство `title` и `icon`.

3. Затем добавьте следующий код JavaScript для добавления на карту меток начальной и конечной точек:

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```
    
    Начальная и конечная точки добавляются в источник данных. Ограничивающий прямоугольник для начальной и конечной точек вычисляется с использованием функции `atlas.data.BoundingBox.fromData`. Этот ограничивающий прямоугольник используется для формирования представления с камер карты в начальной и конечной точках с помощью функции `map.setCamera`. Для компенсации размеров пикселей значков символов добавляется заполнение.

4. Сохраните файл и обновите браузер, чтобы на карте отобразились пометки. Теперь на карте будет крупным планом показан Сиэтл. Вы можете видеть круглую голубую пометку, обозначающую начальную точку, и голубую пометку, обозначающую конечную точку.

   ![Просмотр карт с начальной и конечной точкой](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Построение маршрутов учетом приоритета способа перемещения

В этом разделе показано, как использовать API службы построения маршрутов "Карты" для поиска нескольких маршрутов из заданной начальной точки к точке назначения в соответствии со способом перемещения. Служба построения маршрутов предоставляет интерфейсы API для планирования самого *быстрого*, *краткого*, *экономичного* или *захватывающего* маршрута между двумя расположениями с текущими условиями трафика. Она также позволяет пользователям планировать маршруты в будущем с помощью обширной базы данных Azure, содержащей исторический трафик, и прогнозирования длительности маршрутов в любой день и любое время. Дополнительные сведения см. в статье о [получении направлений маршрута](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Все приведенные ниже блоки кода необходимо добавить в **блок загрузки карты eventListener**. Это позволит гарантировать их загрузку после полной загрузки карты.

1. Создайте клиент службы, добавив следующий код Javascript в обработчик событий загрузки карты.

    ```JavaScript
    //If the service client hasn't been created, create an instance of it.
    if (!client) {
        client = new atlas.service.Client(atlas.getSubscriptionKey());
    }
    ```
    
2. Добавьте следующий блок кода, чтобы создать строку запроса на поиск маршрута.

    ```JavaScript
    //Create the route request with the query being the start and end point in the format 'startLongitude,startLatitude:endLongitude,endLatitude'.
    var routeQuery = startPoint.geometry.coordinates[1] +
        ',' +
        startPoint.geometry.coordinates[0] +
        ':' +
        endPoint.geometry.coordinates[1] +
        ',' +
        endPoint.geometry.coordinates[0];
    ```

3. Добавьте следующий код JavaScript, чтобы запросить маршрут для грузовика с грузом типа USHazmatClass2 и отобразить результаты:

    ```JavaScript
    //Execute the truck route query then add the route to the map.
    client.route.getRouteDirections(routeQuery, {
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```
    Приведенный выше фрагмент кода создает запрос к службе построения маршрутов Azure Maps с помощью метода [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest), а затем с помощью метода [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routegeojson?view=azure-iot-typescript-latest) анализирует ответ в формате GeoJSON. Затем он создает массив координат для возвращаемого маршрута и добавляет его в источник данных. Кроме того, он добавляет индекс 0, чтобы маршрут отображался перед любыми другими линиями в источнике данных. Это необходимо, так как расчет маршрута грузовика часто будет выполняться медленнее, чем расчет маршрута легкового автомобиля, и если линия маршрута грузовика добавляется в источник данных после маршрута легкового автомобиля, она будет показана над ним. К линии маршрута грузовика добавляются два свойства: цвет линии (синий) и ее ширина (9 пикселей). 

4. Добавьте следующий код JavaScript, чтобы запросить маршрут для легкового автомобиля и отобразить результаты.

    ```JavaScript
    //Execute the car route query then add the route to the map.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source.
        datasource.add(routeLine);
    });
    ```
    В этом фрагменте кода для автомобиля используется тот же запрос маршрута грузовика. С помощью метода [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest) создается запрос к службе построения маршрутов Azure Maps, а затем с помощью метода [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routegeojson?view=azure-iot-typescript-latest) анализируется ответ в формате GeoJSON. Затем он создает массив координат для полученного маршрута и добавляет его в источник данных. К линии маршрута легкового автомобиля добавляются два свойства: цвет линии (сиреневый) и ее ширина (5 пикселей). 

5. Сохраните файл **MapTruckRoute.html** и обновите браузер, чтобы просмотреть результат. Для успешного подключения к интерфейсам API службы "Карты Azure" должна использоваться карта следующего вида.

    ![Поиск маршрутов с учетом приоритета с помощью службы построения маршрутов Azure](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Линия маршрута грузовика голубая и более толстая, а линия маршрута легкового автомобиля сиреневая и более тонкая. Маршрут легкового автомобиля проходит через туннели на озере Вашингтон (федеральная автострада номер 90), которые расположены в жилых районах. Поэтому здесь запрещается перевозить опасные отходы. Маршрут грузовика, указывающий тип груза USHazmatClass2, использует другую магистраль.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * создание веб-страницы с помощью API элементов управления картой;
> * Визуализация потока трафика на карте.
> * Создание запросов маршрута по видам транспорта.
> * Отображение нескольких маршрутов на карте.

Пример кода, используемый при работе с этим руководством, приведен здесь:

> [Определение нескольких маршрутов с помощью Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

[См. этот пример в интерактивном режиме](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

В этом руководстве показано, как создать простой указатель магазинов с помощью Azure Maps.

> [!div class="nextstepaction"]
> [Создание указателя магазинов с помощью Azure Maps](./tutorial-create-store-locator.md)
