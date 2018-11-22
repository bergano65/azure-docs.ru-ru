---
title: Поиск маршрута с помощью службы "Карты Azure" | Документация Майкрософт
description: Поиск маршрута к точке интереса с помощью службы "Карты Azure"
author: walsehgal
ms.author: v-musehg
ms.date: 11/14/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a3807dc792c2e56c3e7c1b74f7d3e8f73ac0f4b0
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705095"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Поиск маршрута к точке интереса с помощью службы "Карты Azure"

В этом руководстве показано, как использовать учетную запись службы "Карты Azure" и пакет SDK службы построения маршрутов, чтобы найти маршрут к объекту. Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание веб-страницы с помощью API элементов управления картой;
> * Настройка координат адреса.
> * Отправка запроса к службе построения маршрутов поиска маршрута к объекту.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем продолжить, выполните действия из предыдущего руководства, чтобы [создать учетную запись службы "Карты Azure"](./tutorial-search-location.md#createaccount) и [получить для нее ключ подписки](./tutorial-search-location.md#getkey).

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Создание карты

Чтобы создать статическую HTML-страницу со встроенным API элементов управления картой, сделайте следующее.

1. На локальном компьютере создайте файл **MapRoute.html**.
2. Добавьте в него следующие компоненты HTML.

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
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

            #map {
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

    `atlas.Map` предоставляет элемент управления для визуальной интерактивной веб-карты и является компонентом API Azure Map Control.

4. Сохраните файл и откройте его в браузере. На этом этапе у вас есть базовая карта, которую можно будет дополнительно доработать.

   ![Просмотр базовой карты](./media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Определение способа отображения маршрута

В этом руководстве простой маршрут будет отображаться с использованием значков символов для обозначения начальной и конечной точек и линии для обозначения пути маршрута.

1. В функции GetMap после инициализации карты добавьте следующий код JavaScript.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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
    
    На карту добавляется событие загрузки, которое сработает, когда ресурсы карты будут полностью загружены. В обработчике событий загрузки карты создается источник данных для хранения линии маршрута, а также начальной и конечной точек. Слой линий создается и привязывается к источнику данных, чтобы определить, как будет отображаться линия маршрута. Маршрут будет отображаться в виде синей линии шириной 5 пикселей с закругленными соединениями и концами. Чтобы на этом слое отображались только данные GeoJSON LineString, добавляется фильтр. При добавлении слоя карты передается второй параметр со значением `'labels'`, в котором указывается, что этот слой будет отображаться под метками карты. Это гарантирует, что линия маршрута не будет закрывать дорожные метки. Слой символов создается и привязывается к источнику данных. Этот слой определяет, как будут отображаться начальная и конечная точки. В этом случае были добавлены выражения для извлечения изображения значка и текстовых подписей из свойств в каждом объекте точки. 
    
2. В этом руководстве задайте в качестве начальной точки офис корпорации Майкрософт, а в качестве конечной — заправку в Сиэтле. В обработчике событий загрузки карты добавьте следующий код.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: 'Microsoft',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: 'Contoso Oil & Gas',
        icon: 'pin-blue'
    });    
    ```

    Этот код создает два [объекта точки GeoJSON](https://en.wikipedia.org/wiki/GeoJSON), представляющих начальную и конечную точки маршрута. В каждую точку добавляется свойство `title` и `icon`.
    
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
    
    Начальная и конечная точки добавляются в источник данных. Ограничивающий прямоугольник для начальной и конечной точек вычисляется с использованием функции `atlas.data.BoundingBox.fromData`. Этот ограничивающий прямоугольник используется для формирования представления с камер карты в начальной и конечной точках с помощью функции **map.setCamera**. Для компенсации размеров пикселей значков символов добавляется заполнение.

3. Сохраните файл **MapRoute.html** и обновите страницу в браузере. Теперь на карте будет крупным планом показан Сиэтл. Вы можете видеть круглую голубую пометку, обозначающую начальную точку, и голубую пометку, обозначающую конечную точку.

   ![Просмотр карт с помеченными начальной и конечной точкой](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Получение направлений

В этом разделе показано, как использовать API службы построения маршрутов "Карты" для поиска маршрута из заданной начальной точки к точке назначения. Служба построения маршрутов предоставляет интерфейсы API для планирования самого *быстрого*, *краткого*, *экономичного* или *захватывающего* маршрута между двумя расположениями. Она также позволяет пользователям планировать маршруты в будущем с помощью обширной базы данных Azure, содержащей исторический трафик, и прогнозирования длительности маршрутов в любой день и любое время. Дополнительные сведения см. в статье [Route — Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Маршрут. Получение направления маршрута). Все приведенные ниже функции необходимо добавить в **блок загрузки карты eventListener**. Это позволит гарантировать их загрузку после полной загрузки карты.

1. Создайте клиент службы, добавив следующий код Javascript в обработчик событий загрузки карты.

    ```JavaScript
    //If the service client hasn't already been created, create an instance.
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

3. Чтобы получить маршрут, добавьте в скрипт приведенный ниже блок кода. В этом коде с помощью метода [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) создается запрос к службе построения маршрутов Azure Maps, а затем с помощью метода [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) анализируется ответ в формате GeoJSON. Затем линия маршрута добавляется в ответ к источнику данных, который автоматически отображает ее на карте.

    ```JavaScript
    //Execute the car route query then add the route to the map once a response is received.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Add the route line to the data source.
        datasource.add(geoJsonResponse.getGeoJsonRoutes().features[0]);
    });
    ```

5. Сохраните файл **MapRoute.html** и обновите страницу в браузере. Для успешного подключения к интерфейсам API службы "Карты Azure" должна использоваться карта следующего вида.

    ![Azure Map Control и служба построения маршрутов](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * создание веб-страницы с помощью API элементов управления картой;
> * Настройка координат адреса.
> * Отправка запроса к службе построения маршрутов для поиска маршрута к точке интереса.

Пример кода, используемый при работе с этим руководством, приведен здесь:

> [Поиск маршрута с помощью службы Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

[См. этот пример в интерактивном режиме](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

В следующем руководстве показано, как создать запрос на поиск маршрута с такими ограничениями, как вид транспорта или тип груза, а затем отобразить несколько маршрутов на одной карте.

> [!div class="nextstepaction"]
> [Поиск маршрутов для различных способов перемещения с помощью службы "Карты Azure"](./tutorial-prioritized-routes.md)
