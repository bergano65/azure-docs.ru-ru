---
title: Как использовать пространственный модуль IO Azure Maps Карты Microsoft Azure
description: Узнайте, как использовать модуль Spatial IO, предоставляемый Web SDK Azure Maps. Этот модуль предоставляет надежные функции, чтобы разработчикам было проще интегрировать пространственные данные с веб-sdk Azure Maps.
author: philmea
ms.author: philmea
ms.date: 02/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 1de9dd9721700418b1aa9ba661fc070db1dbedcc
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804645"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Как использовать модуль пространственного IO Карты Azure

Веб-SDK Azure Maps предоставляет **модуль пространственного иу-си,** который интегрирует пространственные данные с веб-SDK Azure Maps с помощью JavaScript или TypeScript. Надежные функции в этом модуле позволяют разработчикам:

- [Прочитайте и напишите общие файлы пространственных данных.](spatial-io-read-write-spatial-data.md) Поддерживаемые форматы файлов включают в себя: KML, КМЗ, GPX, GeoRSS, GML, GeoJSON и CSV файлы, содержащие столбцы с пространственной информацией. Также поддерживает известный текст (WKT).
- [Подключитесь к службам Открытого геопространственного консорциума (OGC) и интегрируйтесь с веб-SDK Azure Maps. Накладывая услуги веб-карты (WMS) и Web Map Tile Services (WMTS) в виде слоев на карте.](spatial-io-add-ogc-map-layer.md)
- [Данные запроса в Службе веб-функций (WFS)](spatial-io-connect-wfs-service.md).
- [Накладывай сложные наборы данных, содержащие информацию о стиле, и автоматически визуалируй с помощью минимального кода.](spatial-io-add-simple-data-layer.md)
- [Использование высокоскоростного XML и делимитированных классов чтения файлов и писателей.](spatial-io-core-operations.md)

В этом руководстве мы узнаем, как интегрировать и использовать модуль Пространственного IO в веб-приложении.

Это видео содержит обзор модуля пространственного IO в Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0" title="Легко интегрировать пространственные данные в Карты Azure - Microsoft Channel 9 Видео"></iframe>


> [!WARNING]
> Используйте только данные и службы, полученные из источника, которому вы доверяете, особенно если ссылаться на них из другого домена. Модуль пространственного IO принимает меры, чтобы свести к минимуму риск, однако самый безопасный подход слишком не позволяет любые danagerous данных в вашем приложении, чтобы начать с. 

## <a name="prerequisites"></a>Предварительные требования

Прежде чем использовать модуль Spatial IO, необходимо [создать учетную запись Azure Maps](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps) и получить [основной ключ подписки для вашей учетной записи.](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account)

## <a name="installing-the-spatial-io-module"></a>Установка модуля пространственного IO

Загрузить пространственный модуль IO Azure Maps можно, используя один из двух вариантов:

* Глобально размещенный Azure CDN для пространственного IO-модуля Azure Maps. Для этого параметра вы добавляете ссылку `<head>` на JavaScript в элементе HTML-файла.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* Исходный код [для azure-maps-spatial-io](https://www.npmjs.com/package/azure-maps-spatial-io) можно загрузить локально, а затем размещать с вашим приложением. Этот пакет также включает определения TypeScript. Для этой опции используйте следующую команду для установки пакета:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Затем добавьте ссылку на JavaScript в `<head>` элемент html документа:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>Использование модуля пространственного IO

1. Создайте HTML-файл.

2. Загрузите Web SDK Azure Maps Web и инициализируют управление картой. Подробности смотрите руководство по [управлению картами Azure Maps.](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) Как только вы закончите с этим шагом, ваш HTML файл должен выглядеть следующим образом:

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. Загрузите пространственный io-модуль Azure Maps. Для этого упражнения используйте CDN для пространственного IO-модуля Azure Maps. Добавьте ссылку `<head>` ниже на элемент вашего HTML-файла:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Инициализируем `datasource`, и добавить источник данных на карту. Инициализируем `layer`, и добавить источник данных на слой карты. Затем отобрадите как источник данных, так и слой. Прежде чем прокрутить вниз, чтобы увидеть полный код на следующем этапе, подумайте о лучших местах для размещения фрагментов исхода данных и фрагментов кода слоя. Напомним, что, прежде чем мы программно манипулировать картой, мы должны ждать, пока карта ресурс готов.

    ```javascript
    var datasource, layer;
    ```

    и

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. Если все сложить воедино, то ваш HTML-код должен выглядеть следующим кодом. В этом примере показано, как читать файл XML из URL. Затем загрузите и отобразите данные о функциях файла на карте. 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. Не забудьте заменить `<Your Azure Maps Key>` основной ключ. Откройте свой HTML-файл, и вы увидите результаты, аналогичные следующему изображению:

    <center>

    ![Пример пространственных данных](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Дальнейшие действия

Функция, которую мы продемонстрировали здесь, является лишь одной из многих функций, доступных в модуле Spatial IO. Читайте руководства ниже, чтобы узнать, как использовать другие функциональные возможности в пространственном IO модуле:

> [!div class="nextstepaction"]
> [Добавление простого слоя данных](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Чтение и запись пространственных данных](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Добавление слоя карты OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Подключение к службе WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Кредитное использование основных операций](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Детали формата поддерживаемых данных](spatial-io-supported-data-format-details.md)

Ссылайтесь на документацию пространственного IO Azure Maps:

> [!div class="nextstepaction"]
> [Пакет пространственных Иумов Карт Azure](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
