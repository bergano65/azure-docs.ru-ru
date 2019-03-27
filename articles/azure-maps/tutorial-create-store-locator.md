---
title: Создание указателя магазинов с помощью Azure Maps | Документация Майкрософт
description: Создание указателя магазинов с помощью Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 11/15/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: d8256f96a79969103b17047c4ebb55fb140eb0bc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58121117"
---
# <a name="create-a-store-locator-by-using-azure-maps"></a>Создание указателя магазинов с помощью Azure Maps

Это руководство поможет вам создать простой указатель магазинов с помощью Azure Maps. Указатели магазинов сейчас используются довольно широко. Большинство принципов, реализованных в приложении этого типа, применимы и ко многим другим приложениям. Указатели магазинов являются обязательным условием для множества предприятий, занимающихся прямой продажей потребителям. Из этого руководства вы узнаете, как выполнять следующие задачи:
    
> [!div class="checklist"]
> * Создание веб-страницы с помощью API Map Control Azure.
> * Загрузка пользовательских данных из файла и их отображение на карте.
> * Поиск адреса или ввод запроса с помощью службы поиска Azure Maps.
> * Получение данных расположения пользователя из браузера и их отображение на карте.
> * Объединение нескольких слоев для создания пользовательских символов на карте.  
> * Группировка точек данных.  
> * Добавление на карту элементов управления масштабом.

<a id="Intro"></a>

Перейдите к [примеру указателя магазинов, работающему в режиме реального времени,](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) или к [исходному коду](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator). 

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этом руководстве, сначала [создайте учетную запись Azure Maps](./tutorial-search-location.md#createaccount) и [получите для нее ключ подписки](./tutorial-search-location.md#getkey).

## <a name="design"></a>Проектирование

Прежде чем перейти к коду, рекомендуем определиться с проектом. Вы можете выбрать для своего указателя магазинов любой уровень сложности. В рамках этого руководства мы создадим простой указатель магазинов. В процессе мы будем давать некоторые советы, которые помогут вам при необходимости расширить функциональность. Мы создадим указатель магазинов для вымышленной компании Contoso Coffee. На приведенном ниже рисунке показан базовый макет указателя магазинов, который мы создаем в рамках этого руководства.

<br/>
<center>

![Макет указателя магазинов-кафе Contoso Coffee](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)</center>

Чтобы этот указатель магазинов был как можно функциональнее, мы использовали гибкий макет, который адаптируется к ширине экрана пользователя, если она составляет менее 700 пикселей. Благодаря адаптивному макету с указателем магазинов удобно работать на небольшом экране, например на мобильном устройстве. Ниже показан макет для небольшого экрана.  

<br/>
<center>

![Макет указателя магазинов Contoso Coffee на мобильном устройстве](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</center>

На макетах представлено довольно простое приложение. В приложении реализованы поле поиска, список ближайших магазинов, карта с определенными метками (символами) и всплывающее окно, в котором отображаются дополнительные сведения, когда пользователь выбирает маркер. Ниже приведено более подробное описание функций, которые мы добавляем в указатель магазинов в рамках этого руководства:

* На карту загружаются все расположения из импортированного файла данных, разделенного табуляциями.
* Пользователь может сдвигать и увеличивать карту, выполнять поиск и пользоваться кнопкой GPS My Location (Мое местонахождение).
* Макет страницы адаптируется к ширине экрана устройства.  
* В качестве заголовка используется логотип магазина.  
* С помощью поля поиска и кнопки поиска пользователь может искать расположения, такие как адрес, почтовый индекс или город. 
* Событие `keypress`, добавленное в поле поиска, активирует поиск, если пользователь нажимает клавишу ВВОД. Эту функцию часто игнорируют, но она делает решение более удобным для пользователя.
* При перемещении карты вычисляется расстояние до каждого расположения от ее центра. Список результатов обновляется, и в верхней части карты отображаются ближайшие расположения.  
* При выборе результата в списке карта центрируется по выбранному расположению и во всплывающем окне отображаются сведения о расположении.  
* При выборе определенного расположения на карте также активируется всплывающее окно.
* Когда пользователь уменьшает карту, расположения группируются в кластеры. Кластеры представлены кругами. Внутри круга отображается количество расположений. Кластеры формируются и разделяются по мере уменьшения или увеличения масштаба.
* При выборе кластера карта увеличивается на два уровня и центрируется по его расположению.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Создание набора данных расположений магазинов

Прежде чем разрабатывать указатель магазинов, нужно создать набор данных магазинов, которые необходимо отобразить на карте. В рамках этого руководства мы используем набор данных для вымышленной сети магазинов-кафе Contoso Coffee. Управление набором данных для этого простого указателя магазинов осуществляется в книге Excel. Набор данных содержит сведения о 10 213 расположениях магазинов-кафе Contoso Coffee в девяти странах: США, Канаде, Великобритании, Франции, Германии, Италии, Нидерландах, Дании и Испании. Приведенный ниже снимок экрана поможет составить представление об этих данных.

<br/>
<center>

![Снимок экрана с данными указателя магазинов в книге Excel](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)</center>

Вы можете [скачать книгу Excel](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). 

Как видно на этом снимке экрана:
    
* Сведения о расположении хранятся в столбцах **AddressLine**, **City**, **Municipality** (округ), **AdminDivision** (область, республика, край), **PostCode** (почтовый индекс) и **Country**.  
* Столбцы **Latitude** и **Longitude** содержат координаты каждого магазина-кафе Contoso Coffee. Если у вас нет сведений о координатах, определить их можно с помощью служб поиска в Azure Maps.
* Некоторые дополнительные столбцы содержат метаданные, связанные с магазинами-кафе: номер телефона, столбцы логических значений для точек доступа Wi-Fi и возможности посещения в креслах-колясках, время открытия и закрытия в 24-часовом формате. Вы можете создать собственные столбцы, которые будут содержать метаданные, более соответствующие вашим расположениям.

> [!Note]
> Azure Maps преобразовывает данные для просмотра в сферическую систему координат проекции Меркатора (EPSG: 3857), но считывает данные в формате EPSG: 4325, для которого используется система WGS84. 

Предоставить набор данных в приложении можно различными способами. Например, вы можете загрузить данные в базу данных и предоставить веб-службу, которая обращается к ним и отправляет результаты в браузер пользователя. Этот вариант идеально подходит для больших или часто обновляемых наборов данных. Но он требует гораздо больше усилий при разработке и обходится намного дороже. 

Другой подход заключается в том, чтобы преобразовать набор данных в обычный текстовый файл, который браузер сможет легко анализировать. Сам файл можно разместить вместе с остальной частью приложения. Этот вариант прост, но он подходит только для небольших наборов данных, так как пользователь загружает все данные, которые есть в наличии. Мы используем для этого набора данных обычный текстовый файл, так как размер файла данных не превышает 1 МБ.  

Чтобы преобразовать книгу в обычный текстовый формат, сохраните ее в файле, разделенном табуляциями. Каждый столбец отделяется символом табуляции, что позволяет легко анализировать столбцы в нашем коде. Можно использовать формат с разделителями-запятыми (CSV), но этот вариант требует больше логики синтаксического анализа. Любое поле, рядом с которым есть запятая, будет заключено в кавычки. Чтобы экспортировать эти данные в виде файла, разделенного табуляциями, в Excel, выберите **Сохранить как**. В раскрывающемся списке **Тип сохраняемого файла** выберите **Текст (разделитель — табуляция)(*.txt)**. Назовите файл *ContosoCoffee.txt*. 

<br/>
<center>

![Снимок экрана окна диалогового окна "Тип сохраняемого файла"](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)</center>

Если открыть текстовый файл в блокноте, он будет выглядеть примерно так:

<br/>
<center>

![Снимок экрана файла блокнота, который содержит набор данных, разделенный табуляциями](./media/tutorial-create-store-locator/StoreDataTabFile.png)</center>


## <a name="set-up-the-project"></a>Настройка проекта

Вы можете создать проект с помощью [Visual Studio](https://visualstudio.microsoft.com) или любого редактора кода по своему усмотрению. В папке проекта создайте три файла: *index.html*, *index.css* и *index.js*. Эти файлы определяют макет, стиль и логику для приложения. Создайте папку с именем *data* и добавьте в нее *ContosoCoffee.txt*. Создайте еще одну папку с именем *images*. В этом приложении мы используем десять изображений для значков, кнопок и маркеров на карте. Вы можете [скачать эти изображения](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). Папка проекта теперь должна выглядеть примерно так:

<br/>
<center>

![Снимок экрана папки проекта простого указателя магазинов](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)</center>

## <a name="create-the-user-interface"></a>Создание пользовательского интерфейса

Чтобы создать пользовательский интерфейс, добавьте код в *index.html*:

1. Добавьте указанные ниже теги `meta` в `head` файла *index.html*. Теги определяют набор символов (UTF-8), указывают Internet Explorer и Microsoft Edge использовать последние версии браузеров и определяют окно просмотра, которое подходит для адаптивных макетов.

    ```HTML
    <meta charset="utf-8" /> 
    <meta http-equiv="x-ua-compatible" content="IE=Edge" /> 
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    ```

1. Добавьте ссылки на файлы JavaScript и CSS для веб-элементов управления Azure Maps.

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" /> 
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script> 
    ```
    
1. Добавьте ссылку на модуль служб Azure Maps. Модуль — это библиотека JavaScript, которая является оболочкой для служб REST Azure Maps и обеспечивает удобство их использования в JavaScript. Этот модуль полезен для поддержки функций поиска.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2"></script>
    ```
    
1. Добавьте ссылки на *index.js* и  *index.css*.

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css" /> 
    <script src="index.js"></script>
    ```
    
1. Добавьте тег `header` в текст документа. Внутри тега `header` добавьте логотип и название компании.

    ```HTML
    <header> 
        <img src="images/Logo.png" /> 
        <span>Contoso Coffee</span> 
    </header>
    ```

1. Добавьте тег `main` и создайте панель поиска с текстовым полем и кнопкой поиска. Также добавьте ссылки `div` для карты, панели списка и кнопки GPS My Location (Мое местонахождение).

    ```HTML
    <main> 
        <div class="searchPanel"> 
            <div> 
                <input id="searchTbx" type="search" placeholder="Find a store" /> 
                <button id="searchBtn" title="Search"></button> 
            </div> 
        </div> 

        <div id="listPanel"></div> 

        <div id="myMap"></div> 

        <button id="myLocationBtn" title="My Location"></button> 
    </main>
    ```

По завершении файл *index.html* должен выглядеть приблизительно как [этот пример файла index.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html).

На следующем этапе нужно определить стили CSS. Стили CSS определяют расположение компонентов приложения и его внешний вид. Откройте файл *index.css* и добавьте в него приведенный ниже код. Стиль `@media` определяет альтернативные параметры стиля, если ширина экрана составляет менее 700 пикселей.  

```css
    html, body { 
        padding: 0; 
        margin: 0; 
        font-family: Gotham, Helvetica, sans-serif; 
        overflow-x: hidden; 
    } 

    header { 
        width: calc(100vw - 10px); 
        height: 30px; 
        padding: 15px 0 20px 20px; 
        font-size: 25px; 
        font-style: italic; 
        font-family: "Comic Sans MS", cursive, sans-serif; 
        line-height: 30px; 
        font-weight: bold;     
        color: white; 
        background-color: #007faa; 
    } 

    header span { 
        vertical-align: middle; 
    } 

    header img { 
        height: 30px; 
        vertical-align: middle; 
    } 

    .searchPanel { 
        position: relative; 
        width: 350px; 
    } 

        .searchPanel div { 
            padding: 20px; 
        } 

        .searchPanel input { 
            width: calc(100% - 50px); 
            font-size: 16px; 
            border: 0; 
            border-bottom: 1px solid #ccc; 
        } 

    #listPanel { 
        position: absolute; 
        top: 135px; 
        left: 0px; 
        width: 350px; 
        height: calc(100vh - 135px); 
        overflow-y: auto; 
    } 

    #myMap { 
        position: absolute; 
        top: 65px; 
        left: 350px; 
        width: calc(100vw - 350px); 
        height: calc(100vh - 65px); 
    } 

    .statusMessage { 
        margin: 10px; 
    } 

    #myLocationBtn, #searchBtn { 
        margin: 0; 
        padding: 0; 
        border: none; 
        border-collapse: collapse; 
        width: 32px; 
        height: 32px; 
        text-align: center; 
        cursor: pointer; 
        line-height: 32px; 
        background-repeat: no-repeat; 
        background-size: 20px; 
        background-position: center center; 
        z-index: 200;     
    } 

    #myLocationBtn { 
        position: absolute; 
        top: 150px; 
        right: 10px; 
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16); 
        background-color: white; 
        background-image: url("images/GpsIcon.png"); 
    } 

        #myLocationBtn:hover { 
            background-image: url("images/GpsIcon-hover.png"); 
        } 

    #searchBtn { 
        background-color: transparent; 
        background-image: url("images/SearchIcon.png"); 
    } 

        #searchBtn:hover { 
            background-image: url("images/SearchIcon-hover.png"); 
        } 

    .listItem { 
        height: 50px; 
        padding: 20px; 
        font-size: 14px; 
    } 

        .listItem:hover { 
            cursor: pointer; 
            background-color: #f1f1f1; 
        } 

    .listItem-title { 
        color: #007faa; 
        font-weight: bold; 
    } 

    .storePopup { 
        min-width: 150px;   
    } 

        .storePopup .popupTitle { 
            border-top-left-radius: 4px; 
            border-top-right-radius: 4px; 
            padding: 8px; 
            height: 30px; 
            background-color: #007faa; 
            color: white; 
            font-weight: bold; 
        } 

        .storePopup .popupSubTitle { 
            font-size: 10px; 
            line-height: 12px; 
        } 

        .storePopup .popupContent { 
            font-size: 11px; 
            line-height: 18px; 
            padding: 8px; 
        } 

        .storePopup img { 
            vertical-align:middle; 
            height: 12px; 
            margin-right: 5px;     
        } 

    /* Adjust the layout of the page when the screen width is less than 700 pixels. */ 
    @media screen and (max-width: 700px) { 
        .searchPanel { 
            width: 100vw; 
        } 

        #listPanel { 
            top: 385px; 
            width: 100%; 
            height: calc(100vh - 385px); 
        } 

        #myMap { 
            width: 100vw; 
            height: 250px; 
            top: 135px; 
            left: 0px; 
        } 

        #myLocationBtn { 
            top: 220px; 
        } 
    } 

    .mapCenterIcon { 
        display: block; 
        width: 10px; 
        height: 10px; 
        border-radius: 50%; 
        background: orange; 
        border: 2px solid white;     
        cursor: pointer;     
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);     
        animation: pulse 3s infinite;     
    } 

    @keyframes pulse { 
        0% {     
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4); 
        } 

        70% { 
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);     
        } 

        100% { 
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0); 
        } 
    }

```

При запуске приложения вы увидите заголовок, поле поиска и кнопку поиска. Но карта не отобразится, так как она еще не загружена. При попытке поиска ничего не происходит. Чтобы получить доступ ко всем функциям указателя магазинов, нужно настроить логику JavaScript, описанную в следующем разделе.

## <a name="wire-the-application-with-javascript"></a>Связывание приложения с JavaScript

На этом этапе в пользовательском интерфейсе установлено все необходимое. Теперь нам нужно добавить код JavaScript для загрузки и анализа данных и их последующей визуализации на карте. Сначала откройте файл *index.js* и добавьте в него код в соответствии с приведенными ниже инструкциями.

1. Добавьте глобальные параметры, чтобы параметры было проще обновлять. Также определите переменные для карты, всплывающего окна, источника данных, слоя значков, маркера HTML, указывающего на центр области поиска, и экземпляра клиента поисковой службы Azure Maps.

    ```Javascript
    //The maximum zoom level to cluster data point data on the map. 
    var maxClusterZoomLevel = 11; 

    //The URL to the store location data. 
    var storeLocationDataUrl = 'data/ContosoCoffee.txt'; 

    //The URL to the icon image. 
    var iconImageUrl = 'images/CoffeeIcon.png'; 
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. Добавьте код в *index.js*. Приведенный ниже код позволяет инициализировать карту, добавить [прослушиватель событий](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events), который ожидает завершения загрузки страницы, связать события для мониторинга загрузки карты и использовать кнопку поиска и кнопку My Location (Мое местонахождение). 

   Когда пользователь нажимает кнопку поиска или клавишу ВВОД, указав в поле поиска данные расположения, инициируется поиск нечетких соответствий по запросу пользователя. Передайте значения ISO-2 в массиве стран для параметра `countrySet`, чтобы ограничить результаты поиска этими странами. Ограничив список стран для поиска, можно получить более точные результаты. 
  
   По завершении поиска установите камеру карты над областью, указанной в первом результате. Когда пользователь нажимает кнопку My Location (Мое местонахождение), чтобы получить сведения о его местонахождении и центрировать по ним карту, используется встроенный в браузер API геолокации HTML5.  

   > [!Tip]
   > Если вы используете всплывающие окна, рекомендуем создать отдельный экземпляр `Popup` и повторно использовать его, обновив содержимое и расположение. Для каждого экземпляра `Popup`, который вы добавляете в код, на страницу добавляется несколько элементов модели DOM. Чем больше на странице элементов модели DOM, тем больше объектов нужно отслеживать браузеру. Если элементов слишком много, браузер может работать медленно.

    ```Javascript
    function initialize() { 
        //Initialize a map instance. 
        map = new atlas.Map('myMap', { 
            center: [-90, 40], 
            zoom: 2,

            //Add your Azure Maps subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        }); 

        //Create a pop-up window, but leave it closed so we can update it and display it later. 
        popup = new atlas.Popup(); 

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());
    
        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 }, // Retry options
        });
    
        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in. 
        document.getElementById('searchBtn').onclick = performSearch; 

        //If the user presses Enter in the search box, perform a search. 
        document.getElementById('searchTbx').onkeyup = function (e) {
            if (e.keyCode === 13) { 
                performSearch(); 
            } 
        }; 

        //If the user selects the My Location button, use the Geolocation API to get the user's location. Center and zoom the map on that location. 
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation; 

        //Wait until the map resources are ready.
        map.events.add('ready', function () { 

        //Add your post-map load functionality. 

        }); 
    } 

    //Create an array of country ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];       

    function performSearch() { 
        var query = document.getElementById('searchTbx').value; 

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();
    
            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            } 
        });
    } 

    function setMapToUserLocation() { 
        //Request the user's location. 
        navigator.geolocation.getCurrentPosition(function (position) { 
            //Convert the Geolocation API position to a longitude and latitude position value that the map can interpret and center the map over it. 
            map.setCamera({ 
                center: [position.coords.longitude, position.coords.latitude], 
                zoom: maxClusterZoomLevel + 1 
            }); 
        }, function (error) { 
            //If an error occurs when the API tries to access the user's position information, display an error message. 
            switch (error.code) { 
                case error.PERMISSION_DENIED: 
                    alert('User denied the request for geolocation.'); 
                    break; 
                case error.POSITION_UNAVAILABLE: 
                    alert('Position information is unavailable.'); 
                    break; 
                case error.TIMEOUT: 
                    alert('The request to get user position timed out.'); 
                    break; 
                case error.UNKNOWN_ERROR: 
                    alert('An unknown error occurred.'); 
                    break; 
            } 
        }); 
    } 

    //Initialize the application when the page is loaded. 
    window.onload = initialize;
    ```

1. В прослушивателе событий `load` карты добавьте элемент управления масштабом и маркер HTML, указывающий на центр области поиска.

    ```Javascript
    //Add a zoom control to the map. 
    map.controls.add(new atlas.control.ZoomControl(), { 
        position: 'top-right'
    }); 

    //Add an HTML marker to the map to indicate the center to use for searching. 
    centerMarker = new atlas.HtmlMarker({ 
        htmlContent: '<div class="mapCenterIcon"></div>', 
        position: map.getCamera().center 
    });

    map.markers.add(centerMarker);
    ```

1. В прослушивателе событий `load` карты добавьте источник данных. Затем выполните вызов для загрузки и анализа набора данных. Включите группировку в источнике данных. Группировка в источнике данных позволяет объединить перекрывающиеся точки в кластер. Когда пользователь увеличивает карту, кластеры разделяются на отдельные точки. Это повышает гибкость пользовательского интерфейса и общую производительность.

    ```Javascript
    //Create a data source, add it to the map, and then enable clustering. 
    datasource = new atlas.source.DataSource(null, { 
        cluster: true, 
        clusterMaxZoom: maxClusterZoomLevel - 1 
    }); 

    map.sources.add(datasource); 

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. Когда вы загрузите набор данных в прослушиватель событий `load` карты, определите набор слоев для отображения данных. Слой пузырьков используется для отображения сгруппированных точек данных. Слой символов над слоем пузырьков позволяет визуализировать количество точек в каждом кластере. Второй слой символов предназначен для отображения пользовательских значков для отдельных расположений на карте. 

   Добавьте события `mouseover` и `mouseout` в слои пузырьков и значков, чтобы при наведении указателя мыши на кластер или значок на карте изменялся курсор. Добавьте событие `click` в слой пузырьков кластера. Это событие `click` позволяет увеличить карту на два уровня и центрировать ее по любому выбранному кластеру. Добавьте событие `click` в слой значков. Это событие `click` позволяет отобразить всплывающее окно со сведениями о магазине-кафе, когда пользователь выбирает значок определенного расположения. Добавьте на карту событие, позволяющее определить, когда прекратится перемещение. При возникновении этого события обновите элементы на панели списка.  

    ```Javascript
    //Create a bubble layer to render clustered data points. 
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, { 
        radius: 12, 
        color: '#007faa', 
        strokeColor: 'white', 
        strokeWidth: 2, 
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property. 
    }); 

    //Create a symbol layer to render the count of locations in a cluster. 
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, { 
        iconOptions: { 
            image: 'none' //Hide the icon image. 
        }, 
        
        textOptions: { 
            textField: '{point_count_abbreviated}', 
            size: 12, 
            font: ['StandardFont-Bold'], 
            offset: [0, 0.4], 
            color: 'white' 
        } 
    }); 

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]); 

    //Load a custom image icon into the map resources.     
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function () {            

    //Create a layer to render a coffee cup symbol above each bubble for an individual location. 
    iconLayer = new atlas.layer.SymbolLayer(datasource, null, { 
        iconOptions: { 
            //Pass in the ID of the custom icon that was loaded into the map resources. 
            image: 'myCustomIcon', 

            //Optionally, scale the size of the icon. 
            font: ['SegoeUi-Bold'], 

            //Anchor the center of the icon image to the coordinate. 
            anchor: 'center', 

            //Allow the icons to overlap. 
            allowOverlap: true 
        }, 

        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer. 
    }); 

    map.layers.add(iconLayer); 

    //When the mouse is over the cluster and icon layers, change the cursor to a pointer. 
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function () { 
        map.getCanvasContainer().style.cursor = 'pointer'; 
    }); 

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab). 
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function () { 
        map.getCanvasContainer().style.cursor = 'grab'; 
    }); 

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function (e) { 
        map.setCamera({ 
            center: e.position, 
            zoom: map.getCamera().zoom + 2 
        }); 
    }); 

    //Add a click event to the icon layer and show the shape that was selected. 
    map.events.add('click', iconLayer, function (e) { 
        showPopup(e.shapes[0]); 
    }); 

    //Add an event to monitor when the map is finished rendering the map after it has moved. 
    map.events.add('render', function () { 
        //Update the data in the list. 
        updateListItems(); 
    });
    ```

1. Когда вы загрузите набор данных магазинов-кафе, сначала скачайте его. Затем разбейте текстовый файл на строки. В первой строке содержатся данные заголовка. Чтобы код было проще воспринимать, выполним анализ заголовка в объекте, с помощью которого затем можно искать индекс ячейки для каждого свойства. Задайте циклический перебор всех строк, кроме первой, и создайте функцию точки. Добавьте функцию точки в источник данных. Наконец, обновите панель списка.

    ```Javascript
    function loadStoreData() { 

    //Download the store location data. 
    fetch(storeLocationDataUrl)     
        .then(response => response.text()) 
        .then(function (text) { 

            //Parse the tab-delimited file data into GeoJSON features. 
            var features = []; 

            //Split the lines of the file. 
            var lines = text.split('\n'); 

            //Grab the header row. 
            var row = lines[0].split('\t'); 

            //Parse the header row and index each column to make the code for parsing each row easier to follow. 
            var header = {}; 
            var numColumns = row.length; 
            for (var i = 0; i < row.length; i++) { 
                header[row[i]] = i; 
            } 

            //Skip the header row and then parse each row into a GeoJSON feature. 
            for (var i = 1; i < lines.length; i++) { 
                row = lines[i].split('\t'); 

                //Ensure that the row has the correct number of columns. 
                if (row.length >= numColumns) { 

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), { 
                        AddressLine: row[header['AddressLine']], 
                        City: row[header['City']], 
                        Municipality: row[header['Municipality']], 
                        AdminDivision: row[header['AdminDivision']], 
                        Country: row[header['Country']], 
                        PostCode: row[header['PostCode']], 
                        Phone: row[header['Phone']], 
                        StoreType: row[header['StoreType']], 
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false, 
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false, 
                        Opens: parseInt(row[header['Opens']]), 
                        Closes: parseInt(row[header['Closes']]) 
                    })); 
                } 
            } 

            //Add the features to the data source. 
            datasource.add(new atlas.data.FeatureCollection(features)); 

            //Initially, update the list items. 
            updateListItems(); 
        }); 
    }
    ```

1. При обновлении панели списка вычисляется расстояние от центра карты до всех функций точек в текущем представлении карты. Затем функции сортируются по расстоянию. Для отображения каждого расположения на панели списка создается HTML-код.

    ```Javascript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>'; 

    function updateListItems() { 
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map. 
        var camera = map.getCamera(); 
        var listPanel = document.getElementById('listPanel'); 

        //Get all the shapes that have been rendered in the bubble layer.  
        var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]); 

        data.forEach(function (shape) { 
            if (shape instanceof atlas.Shape) { 
                //Calculate the distance from the center of the map to each shape, and then store the data in a distance property.  
                shape.distance = atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles'); 
            } 
        }); 

        //Sort the data by distance. 
        data.sort(function (x, y) { 
            return x.distance - y.distance; 
        }); 

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button. 
        if (camera.zoom < maxClusterZoomLevel) { 
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>'; 
        } else { 
            //Update the location of the centerMarker property. 
            centerMarker.setOptions({ 
                position: camera.center, 
                visible: true 
            }); 

            //List the ten closest locations in the side panel. 
            var html = [], properties; 

            /* 
            Generating HTML for each item that looks like this: 
            <div class="listItem" onclick="itemSelected('id')"> 
                <div class="listItem-title">1 Microsoft Way</div> 
                Redmond, WA 98052<br /> 
                Open until 9:00 PM<br /> 
                0.7 miles away 
            </div> 
            */ 

            data.forEach(function (shape) { 
                properties = shape.getProperties(); 
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">', 
                properties['AddressLine'], 
                '</div>', 
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode. 
                getAddressLine2(properties), 
                '<br />', 

                //Convert the closing time to a format that is easier to read. 
                getOpenTillTime(properties), 
                '<br />', 

                //Route the distance to two decimal places.  
                (Math.round(shape.distance * 100) / 100), 
                ' miles away</div>'); 
            }); 

            listPanel.innerHTML = html.join(''); 

            //Scroll to the top of the list panel in case the user has scrolled down. 
            listPanel.scrollTop = 0; 
        } 
    } 

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string. 
    function getOpenTillTime(properties) { 
        var time = properties['Closes']; 
        var t = time / 100; 
        var sTime; 

        if (time === 1200) { 
            sTime = 'noon'; 
        } else if (time === 0 || time === 2400) { 
            sTime = 'midnight'; 
        } else {     
            sTime = Math.round(t) + ':'; 

            //Get the minutes. 
            t = (t - Math.round(t)) * 100; 

            if (t === 0) { 
                sTime += '00'; 
            } else if (t < 10) { 
                sTime += '0' + t; 
            } else { 
                sTime += Math.round(t); 
            } 

            if (time < 1200) { 
                sTime += ' AM'; 
            } else { 
                sTime += ' PM'; 
            } 
        } 

        return 'Open until ' + sTime; 
    } 

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode. 
    function getAddressLine2(properties) { 
        var html = [properties['City']]; 

        if (properties['Municipality']) { 
            html.push(', ', properties['Municipality']); 
        } 

        if (properties['AdminDivision']) { 
            html.push(', ', properties['AdminDivision']); 
        } 

        if (properties['PostCode']) { 
            html.push(' ', properties['PostCode']); 
        } 

        return html.join(''); 
    }
    ```

1. Когда пользователь выбирает элемент на панели списка, из источника данных извлекается фигура, с которой связан элемент. На основе сведений о свойстве, хранящихся в фигуре, создается всплывающее окно. Карта центрируется по фигуре. Если ширина карты менее 700 пикселей, ее представление смещается, чтобы отображалось всплывающее окно.

    ```Javascript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window. 
    function itemSelected(id) { 
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id); 
        showPopup(shape); 

        //Center the map over the shape on the map. 
        var center = shape.getCoordinates(); 
        var offset;
    
        //If the map is less than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }
    
        map.setCamera({
            center: center,
            centerOffset: offset
        });
    } 

    function showPopup(shape) { 
        var properties = shape.getProperties(); 

        /* Generating HTML for the pop-up window that looks like this: 

            <div class="storePopup"> 
                <div class="popupTitle"> 
                    3159 Tongass Avenue 
                    <div class="popupSubTitle">Ketchikan, AK 99901</div> 
                </div> 
                <div class="popupContent"> 
                    Open until 22:00 PM<br/> 
                    <img title="Phone Icon" src="images/PhoneIcon.png"> 
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a> 
                    <br>Amenities: 
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png"> 
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png"> 
                </div> 
            </div> 
        */ 

        var html = ['<div class="storePopup">']; 
        html.push('<div class="popupTitle">', 
            properties['AddressLine'], 
            '<div class="popupSubTitle">', 
            getAddressLine2(properties), 
            '</div></div><div class="popupContent">', 

            //Convert the closing time to a format that's easier to read. 
            getOpenTillTime(properties), 

            //Route the distance to two decimal places.  
            '<br/>', (Math.round(shape.distance * 100) / 100), 
            ' miles away', 
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:', 
            properties['Phone'], 
            '">',  
            properties['Phone'], 
            '</a>' 
        ); 

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) { 
            html.push('<br/>Amenities: '); 
            
            if (properties['IsWiFiHotSpot']) { 
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>') 
            } 

            if (properties['IsWheelchairAccessible']) { 
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>') 
            } 
        } 

        html.push('</div></div>'); 

        //Update the content and position of the pop-up window for the specified shape information. 
        popup.setOptions({ 

            //Create a table from the properties in the feature. 
            content:  html.join(''),     
            position: shape.getCoordinates() 
        }); 

        //Open the pop-up window. 
        popup.open(map); 
    }
    ```

Теперь у вас есть полнофункциональный указатель магазинов. В веб-браузере откройте файл *index.html* для указателя магазинов. Когда на карте отображаются кластеры, вы можете искать расположение с помощью поля поиска, кнопки My Location (Мое местонахождение), а также выбрав кластер или увеличив карту, чтобы просмотреть отдельные расположения.

При первом нажатии кнопки My Location (Мое местонахождение) в браузере отобразится предупреждение системы безопасности с запросом разрешения на доступ к сведениям о местонахождении пользователя. Если пользователь соглашается предоставить общий доступ к сведениям о его местонахождении, карта увеличивается в этой точке и отображаются ближайшие расположения магазинов-кафе. 

<br/>
<center>

![Снимок экрана с запросом браузера на доступ к сведениям о местонахождении пользователя](./media/tutorial-create-store-locator/GeolocationApiWarning.png)</center>

При достаточном увеличении карты в области с расположениями магазинов-кафе кластеры разделяются на отдельные расположения. Выберите один из значков на карте или элемент на боковой панели, чтобы отобразилось всплывающее окно со сведениями об этом расположении.

<br/>
<center>

![Снимок экрана готового указателя магазинов](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)</center>

Если сузить окно браузера менее чем до 700 пикселей или открыть приложение на мобильном устройстве, макет адаптируется к меньшему размеру экрана. 

<br/>
<center>

![Снимок экрана указателя магазинов, адаптированного к небольшому экрану](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)</center>

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как создать базовый указатель магазинов с помощью Azure Maps. Возможно, созданный в рамках этого руководства указатель магазинов будет обладать всеми нужными вам функциями. Но вы можете добавить в указатель магазинов и другие функции или расширить существующие, чтобы персонализировать пользовательский интерфейс: 

> [!div class="checklist"]
> * Включите в поле поиска [предложения при вводе](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI).  
> * Добавьте [поддержку нескольких языков](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization). 
> * Предоставьте пользователю возможность [фильтровать расположения по маршруту](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route). 
> * Добавьте возможность [настраивать фильтры](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property). 
> * Добавьте возможность указывать исходное значение поиска с помощью строки запроса. Если включить этот параметр в указатель магазинов, пользователи смогут создавать закладки для поисковых запросов и совместно использовать их. Кроме того, это обеспечит простой способ передачи поисковых запросов к определенной странице с другой страницы.  
> * Разверните свой указатель магазинов как [веб-приложение Службы приложений Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html). 
> * Храните свои данные в базе данных и выполняйте поиск ближайших расположений. Дополнительные сведения см. в статьях [Основные сведения о типах пространственных данных](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-2017) и [Запросы пространственных данных для ближайшего соседа](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-2017).

Пример кода, используемый при работе с этим руководством, приведен здесь:

> [Создание указателя магазинов с помощью Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)

[См. этот пример в интерактивном режиме](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)

Дополнительные сведения о времени сервисного обслуживания и возможностях службы Azure Maps см. здесь:

> [!div class="nextstepaction"]
> [Уровни увеличения и параметры сетки](zoom-levels-and-tile-grid.md)

Дополнительные примеры кода и сведения о возможностях интерактивного программирования см. здесь:

> [!div class="nextstepaction"]
> [Как использовать библиотеку Map Control в службе "Карты Azure"](how-to-use-map-control.md)
