---
title: Поиск с помощью службы "Карты Azure" | Документация Майкрософт
description: Поиск ближайшей точки интереса с помощью службы "Карты Azure"
author: walsehgal
ms.author: v-musehg
ms.date: 01/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 04ab94d8b0b8f012707bb88a52c44b91063cbe39
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402189"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Поиск ближайшей точки интереса с помощью службы "Карты Azure"

В этом руководстве показано, как настроить учетную запись службы "Карты Azure", а затем использовать API службы "Карты Azure" для поиска точки интереса. Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание учетной записи службы "Карты Azure";
> * получение первичного ключа для учетной записи службы "Карты Azure";
> * создание веб-страницы с помощью API элементов управления картой;
> * использование службы поиска "Карты Azure" для поиска ближайшей точки интереса.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портале Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Создание учетной записи службы "Карты Azure"

Создайте учетную запись службы "Карты Azure" с помощью следующих действий:

1. В верхнем левом углу [портала Azure](https://portal.azure.com) щелкните **Создать ресурс**.
2. В поле *Поиск по Marketplace* введите **Карты**.
3. Выберите *Результаты*, а затем — **Карты**. Нажмите кнопку **Создать**, расположенную под картой.
4. На странице **Создание учетной записи Azure Maps** введите следующие значения:
    * *Имя* новой учетной записи.
    * *Подписку*, которую необходимо использовать для этой учетной записи.
    * Имя *группы ресурсов* для этой учетной записи. Вы *создать новую* или *использовать существующую* группу ресурсов.
    * Выберите *расположение группы ресурсов*.
    * Ознакомьтесь с *лицензией* и *заявлением о конфиденциальности*, а затем установите флажок, чтобы принять условия соглашения.
    * Нажмите кнопку **Создать** .

    ![Создание учетной записи службы "Карты Azure" на портале](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Получение первичного ключа для учетной записи

После успешного создания учетной записи службы "Карты Azure" извлеките ключ, позволяющий запрашивать API службы "Карты Azure".

1. Откройте учетную запись службы "Карты Azure" на портале.
2. В разделе параметров выберите **Ключи**.
3. Скопируйте **первичный ключ** в буфер обмена. Сохраните его локально для использования в этом руководстве позже.

    ![Получение первичного ключа на портале](./media/tutorial-search-location/get-key.png)

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Создание карты

API элементов управления картой — это удобная клиентская библиотека, которая позволяет легко интегрировать службу "Карты Azure" в веб-приложение. Она скрывает сложность "чистых" вызовов службы REST и ускоряет работу с помощью компонентов, поддерживающих изменение стилей и настройку. Чтобы создать статическую HTML-страницу со встроенным API элементов управления картой, сделайте следующее.

1. На локальном компьютере создайте файл **MapSearch.html**.
2. Добавьте в него следующие компоненты HTML.

   ```HTML
   <!DOCTYPE html>
   <html>
   <head>
      <title>Map Search</title>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

      <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
      <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

      <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
      <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>

      <script>      
         var map, datasource, client, popup;

         function GetMap(){
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

3. Добавьте следующий код JavaScript к функции `GetMap` HTML-файла. Замените строку **\<Your Azure Maps Key\>** первичным ключом, скопированным из учетной записи службы Maps.

   ```JavaScript
   //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
   atlas.setSubscriptionKey('<Your Azure Maps Key>');

   //Initialize a map instance.
   map = new atlas.Map('myMap');
   ```

   Этот сегмент инициирует API Map Control для ключа учетной записи службы Azure Maps. **Atlas** — это пространство имен, которое содержит API и связанные визуальные компоненты. **atlas.Map** предоставляет элемент управления для визуальной интерактивной веб-карты. 

4. Сохраните изменения в файл и откройте HTML-страницу в браузере. Это самая простая карта, которую можно создать, вызвав **atlas.map**, используя ключ учетной записи.

   ![Просмотр карты](./media/tutorial-search-location/basic-map.png)

5. В функции `GetMap` после инициализации карты добавьте следующий код JavaScript. 

   ```JavaScript
   //Wait until the map resources have fully loaded.
   map.events.add('load', function () {

      //Create a data source and add it to the map.
      datasource = new atlas.source.DataSource();
      map.sources.add(datasource);

      //Add a layer for rendering point data.
      var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
         iconOptions: {
            iconImage: 'pin-round-darkblue',
            anchor: 'center',
            allowOverlap: true
         }
      });
      map.layers.add(resultLayer);

   });
   ```

   На карту добавляется событие загрузки, которое сработает, когда ресурсы карты будут полностью загружены. В обработчике событий загрузки карты создается источник данных для хранения выходных данных. Слой символов создается и привязывается к источнику данных. Этот слой определяет, как должны отображаться выходные данные в источнике данных. В этом случае — с использованием синего круглого значка, который размещается по центру координат результатов и может перекрываться другими значками. 

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Добавление возможностей поиска

В этом разделе показано, как использовать API службы "Карты Azure" для поиска точки интереса на карте. Это RESTful API, предназначенный для разработчиков. Он позволяет находить адреса, объекты и другие географические сведения. Служба поиска назначает широту и долготу для определенного адреса. С помощью описанного ниже **модуля службы** можно искать расположение, используя API поиска службы "Карты Azure".

### <a name="service-module"></a>Модуль службы

1. В обработчике событий загрузки карты создайте службу-клиент, добавив следующий код JavaScript.

    ```JavaScript
    //Create an instance of the services client.
     client = new atlas.service.Client(atlas.getSubscriptionKey());
    ```

2. Затем, чтобы создать поисковый запрос, добавьте следующий блок сценария. В нем используется служба поиска нечетких соответствий, которая является базовым API поиска Службы поиска. Служба поиска нечетких соответствий обрабатывает большинство нечетких входных данных, например адреса, места и точки интереса (POI). Этот код ищет близлежащие бензозаправочные станции в пределах указанного радиуса. Затем ответ преобразуется в формат GeoJSON и добавляется к источнику данных, после чего данные автоматически отображаются на карте с помощью слоя символов. Последняя часть сценария устанавливает представление с камер на карте, используя ограничивающий прямоугольник с результатами, с помощью свойства карты [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Для компенсации размеров пикселей значков символов добавляется заполнение, так как ограничивающий прямоугольник вычисляется на основе координат. 
 
   ```JavaScript
   //Execute a POI search query then add the results to the map.
    client.search.getSearchPOI('gasoline station', {
        lat: 47.6292,
        lon: -122.2337,
        radius: 100000
    }).then(response => {
        //Parse the response into GeoJSON so that the map can understand.
        var geojsonResponse = new atlas.service.geojson.GeoJsonSearchResponse(response);
        var results = geojsonResponse.getGeoJsonResults();

        //Add the results to the data source so they can be rendered. 
        datasource.add(results);

        // Set the camera bounds
        map.setCamera({
            bounds: results.bbox,
            padding: 50
        });
    });
   ```
 
3. Сохраните файл **MapSearch.html** и обновите страницу в браузере. Теперь на карте будет крупным планом показан Сиэтл, а синие пометки указывают расположение бензозаправочных станций в области.

   ![Просмотр карты с результатами поиска](./media/tutorial-search-location/pins-map.png)

4. Вы можете увидеть необработанные данные, которые отображаются на карте в преобразованном для просмотра виде, введя следующий HTTP-запрос в вашем браузере. Замените \<Your Azure Maps Key\> на первичный ключ.

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<Your Azure Maps Key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

На этом этапе страница MapSearch может отображать расположения точек интереса, возвращаемых из запроса нечеткого поиска. Давайте добавим некоторые интерактивные возможности и дополнительные сведения о расположениях.

## <a name="add-interactive-data"></a>Добавление интерактивных данных

Карта, которую мы создали, учитывает только данные широты и долготы в результатах поиска. Если взглянуть на необработанные данные JSON, которые возвращает служба поиска "Карты Azure", вы увидите, что они содержат дополнительные сведения о каждой заправочной станции, включая название и адрес. Эти данные можно включить в карту с помощью интерактивных всплывающих окон.

1. Добавьте приведенный ниже код в обработчик событий загрузки карты после кода для запроса службы поиска нечетких соответствий. Этот процесс создаст экземпляр всплывающего окна и добавит событие наведения указателя мыши на слой символов.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```
    
    **atlas.Popup** API предоставляет информационное окно, которое можно привязать к нужной позиции на карте. 
      
2. В теге *script* после функции `GetMap` добавьте следующий код для отображения во всплывающем окне событий наведения указателя мыши поверх сведений о результатах. 

   ```JavaScript
   function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occurred on.
        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = ['<div style="padding:5px"><div><b>', p.poi.name,
            '</b></div><div>', p.address.freeformAddress,
            '</div><div>', position[1], ', ', position[0], '</div></div>'];

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html.join(''),
            position: position
        });

        //Open the popup.
        popup.open(map);
   }
   ```

2. Сохраните файл и обновите страницу в браузере. Теперь на карте в браузере при наведении указателя мыши на любой показанный маркер поиска отображаются информационные всплывающие элементы.

    ![Azure Map Control и служба поиска](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Создание учетной записи службы "Карты Azure"
> * Получение первичного ключа для учетной записи
> * Создание веб-страницы с помощью API Map Control.
> * Использование службы поиска для поиска ближайшего объекта.

Пример кода, используемый при работе с этим руководством, приведен здесь:

> [Поиск местоположения с помощью Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

[См. этот пример в интерактивном режиме](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

В следующем руководстве показано, как отобразить маршрут между двумя расположениями.

> [!div class="nextstepaction"]
> [Прокладывание маршрута до точки интереса в службе "Карты Azure"](./tutorial-route-location.md)
