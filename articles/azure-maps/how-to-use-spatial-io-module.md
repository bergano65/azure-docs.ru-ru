---
title: Использование модуля пространственного ввода-вывода Azure Maps | Карты Microsoft Azure
description: Узнайте, как использовать модуль пространственных операций ввода-вывода, предоставляемый Azure Maps Web SDK. Этот модуль предоставляет надежные функции, облегчающие разработчикам интеграцию пространственных данных с веб-пакетом SDK для Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c309473529666d369e8accd1617021249867fb19
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78371042"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Использование модуля пространственного ввода-вывода Azure Maps

Azure Maps веб-пакет SDK предоставляет **модуль пространственного ввода/вывода**, который интегрирует пространственные данные с веб-пакетом SDK Azure Maps с помощью JavaScript или TypeScript. Надежные функции этого модуля позволяют разработчикам:

- [Чтение и запись данных в общие пространственные файлы](spatial-io-read-write-spatial-data.md). В число поддерживаемых форматов файлов входят: КМЛ, КМЗ, GPX), Жеорсс, GML и CSV-файлы, содержащие столбцы с пространственными данными.
- [Подключение к службам Открытый геопространственный консорциум (OGC) и интеграция с Azure Maps веб-пакетом SDK. Перекрытие служб веб-сопоставления (WMS) и служб плиток веб-карты (ВМТС) в качестве слоев на карте.](spatial-io-add-ogc-map-layer.md).
- [Запрос данных в службе веб-компонентов (ВФС)](spatial-io-connect-wfs-service.md).
- [Наложение сложных наборов данных, содержащих сведения о стилях, и их автоматическое отображение](spatial-io-add-simple-data-layer.md).
- [Использование высокоскоростных XML и классов чтения и записи файлов с разделителями](spatial-io-core-operations.md).

В этом руководство мы расскажем, как интегрировать и использовать модуль пространственных операций ввода-вывода в приложении.

## <a name="prerequisites"></a>предварительные требования

Прежде чем использовать модуль пространственных ввода-вывода, необходимо [сделать учетную запись Azure Maps](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps) и [получить ключ первичной подписки для вашей учетной записи](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account).

## <a name="installing-the-spatial-io-module"></a>Установка модуля пространственных операций ввода-вывода

Модуль пространственного ввода-вывода Azure Maps можно загрузить с помощью одного из двух параметров:

* Глобально размещенная Azure CDN для модуля пространственного ввода-вывода Azure Maps. Для этого параметра необходимо добавить ссылку на JavaScript в элементе `<head>` HTML-файла.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* Исходный код для [Azure-Maps-пространственные-IO](https://www.npmjs.com/package/azure-maps-spatial-io) можно загрузить локально, а затем разместить в приложении. Этот пакет также включает определения TypeScript. Для этого параметра используйте следующую команду, чтобы установить пакет:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Затем добавьте ссылку на JavaScript в элементе `<head>` HTML-документа:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>Использование модуля пространственных операций ввода-вывода

1. Создайте HTML-файл.

2. Загрузите веб-пакет SDK Azure Maps и инициализируйте элемент управления картой. Подробные сведения см. в [Azure Mapsном руководством по управлению картой](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) . После завершения этого шага ваш HTML-файл должен выглядеть следующим образом:

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

2. Загрузите модуль пространственного ввода-вывода Azure Maps. В этом упражнении используйте CDN для модуля пространственного ввода-вывода Azure Maps. Добавьте ссылку ниже в элемент `<head>` HTML-файла:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Инициализация `datasource`и Добавление источника данных к сопоставлению. Инициализируйте `layer`и добавьте источник данных на слой-карту. Затем отрисовывается как источник данных, так и слой. Перед прокруткой вниз, чтобы увидеть полный код на следующем шаге, подумайте о лучших местах для размещения фрагментов кода источника данных и слоя. Вспомним, что прежде чем мы программно манипулировать картой, следует подождать, пока ресурс Map не будет готов.

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

4. При этом код HTML должен выглядеть, как в следующем коде. В этом примере показано, как считать XML-файл из URL-адреса. Затем загрузите и отобразите данные о компонентах файла на карте. 

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

5. Не забудьте заменить `<Your Azure Maps Key>` первичным ключом. Откройте HTML-файл, и вы увидите результаты, аналогичные следующему изображению:

    <center>

    ![Пример пространственных данных](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Дальнейшие действия

Рассматриваемая здесь функция является только одной из многих функций, доступных в модуле пространственного ввода-вывода. Ознакомьтесь с приведенными ниже руководствами, чтобы узнать, как использовать другие функции в модуле пространственных операций ввода-вывода.

> [!div class="nextstepaction"]
> [Добавление простого уровня данных](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Чтение и запись пространственных данных](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Добавление слоя OGC Map](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Подключение к службе ВФС](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Использование основных операций](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Сведения о поддерживаемых форматах данных](spatial-io-supported-data-format-details.md)

См. документацию по пространственному вводу-выводу Azure Maps:

> [!div class="nextstepaction"]
> [Пакет Azure Maps пространственных операций ввода-вывода](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
