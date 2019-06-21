---
title: Краткое руководство. PowerShell и REST API — поиска Azure
description: Создания, загрузки и запроса индекса с помощью PowerShell Invoke-RestMethod и API REST службы поиска Azure.
ms.date: 06/10/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 64252f42f5b3690e8ba6d929e27f7f41b649922e
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302293"
---
# <a name="quickstart-create-an-azure-search-index-using-powershell"></a>Краткое руководство. Создание индекса службы поиска Azure с помощью PowerShell
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Python](search-get-started-python.md)
> * [Портал](search-create-index-portal.md)
> 

В этой статье описывается процесс создания, загрузки и запросов в индекс поиска Azure с помощью PowerShell и [API REST службы поиска Azure](https://docs.microsoft.com/rest/api/searchservice/). В этой статье объясняется, как для выполнения команд PowerShell interatively. Кроме того можно выполнить по завершении сценария. Чтобы загрузить копию, перейдите к [azure-search-powershell-samples](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) репозитория.

Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) перед началом, а затем [зарегистрируйтесь в службе "Поиск Azure"](search-create-service-portal.md).

## <a name="prerequisites"></a>Технические условия

В этом кратком руководстве используются перечисленные ниже службы и инструменты. 

+ [PowerShell 5.1 или более поздней версии](https://github.com/PowerShell/PowerShell), с использованием [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) для последовательных и интерактивных шагов.

+ [Создайте службу "Поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. Вы можете использовать бесплатную службу для выполнения инструкций, описанных в этом кратком руководстве. 

## <a name="get-a-key-and-url"></a>Получение ключа и URL-адреса

Вызовам REST требуется URL-адрес службы и ключ доступа при каждом запросе. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **обзора** службы поиска получите URL-адрес. Пример конечной точки может выглядеть так: `https://mydemo.search.windows.net`.

2. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

![Получение конечной точки HTTP и ключа доступа](media/search-fiddler/get-url-key.png "Получение конечной точки HTTP и ключа доступа")

Для выполнения любого запроса к службе требуется использование ключа API. Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

## <a name="connect-to-azure-search"></a>Подключение к Поиску Azure

1. В PowerShell, создать **$headers** объект для сохранения content-type и ключ API. Замените ключ API администратора (YOUR-ADMIN-API-KEY) с ключом, который является допустимым для службы поиска. Необходимо присвоить этому заголовку один раз в течение сеанса, но необходимо добавить в каждый запрос. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Создание **$url** , указывающий службу индексов коллекции. Замените допустимый поиска службы имя службы (YOUR-SEARCH-SERVICE-NAME).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06"
    ```

3. Запустите **Invoke-RestMethod** отправить запрос GET в службу и проверьте подключение. Добавить **ConvertTo-Json** , и вы сможете просматривать ответы, отправляемые службой.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Если служба является пустым и не имеет индексов, результаты будут как в следующем примере. В противном случае вы увидите JSON-представление определений индекса.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1\. Создание индекса

Если вы используете портал, индекс должен существовать в службе перед загрузкой данных. Этот шаг определяет индекс и помещает его в службу. [REST API создания индекса](https://docs.microsoft.com/rest/api/searchservice/create-index) используется для выполнения этого шага.

Обязательные элементы индекса включают имя и коллекцию полей. Коллекция полей определяет структуру *документа*. Каждое поле имеет имя, тип и атрибуты, которые определяют, как он используется (например, является ли это полнотекстового поиска для поиска, фильтрацию или отображалось в результатах поиска). В индексе, одно из полей типа `Edm.String` необходимо назначить в качестве *ключ* для идентификации документа.

Этот индекс имеет имя «hotels-quickstart» и определения полей, указанные ниже. Он является частью более крупной [индекса отелей](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) используется в других пошаговых руководств. Мы усекаются в этом кратком руководстве для краткости.

1. Вставьте этот пример в PowerShell для создания **$body** объект, содержащий схему индекса.

    ```powershell
    $body = @"
    {
        "name": "hotels-quickstart",  
        "fields": [
            {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
            {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
            {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
            {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
            {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
            {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Address", "type": "Edm.ComplexType", 
            "fields": [
            {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
            {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
            ]
         }
      ]
    }
    "@
    ```

2. Задать URI для коллекции индексов в службе и *гостиницы quickstart* индекса.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06"
    ```

3. Выполните команду с **$url**, **$headers**, и **$body** для создания индекса в службе. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Результаты должны выглядеть примерно так (усеченный в первые два поля, для краткости):

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> Для проверки подлинности можно также проверить список индексов на портале.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2\. Загрузка документов

Чтобы отправить документы, используйте запрос HTTP POST к конечной точке URL-адрес вашего индекса. REST API для выполнения этой задачи — [Добавление, обновление и удаление документов](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

1. Вставьте этот пример в PowerShell для создания **$body** объект, содержащий документы, который необходимо передать. 

    Этот запрос содержит две полные резервные копии и частичные записи. Частичные записи показано, вы можете отправить документы неполной. `@search.action` Параметр указывает, как выполняется индексирование. Допустимые значения: отправка, merge, mergeOrUpload и delete. Поведение mergeOrUpload либо создает новый документ для hotelId = 3 или обновляет содержимое, если он уже существует.

    ```powershell
    $body = @"
    {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": 
            {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": 
            {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    "@
    ```

1. Значение конечной точки *гостиницы quickstart* коллекции документов и включить операции с индексом (индексы или гостиницы-краткое руководство/документы/индекса).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06"
    ```

1. Выполните команду с **$url**, **$headers**, и **$body** для загрузки документов в индекс отелей quickstart.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Результаты должны выглядеть аналогично приведенному ниже. Вы должны увидеть [код состояния 201](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
        "value":  [
                    {
                        "key":  "1",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "2",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "3",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3\. Поиск в индексе

Этот шаг показывает, как запросить индекс с помощью [API поиска документов](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Не забудьте использовать одинарные кавычки для поиска $urls. Включить строки запросов **$** символов и можно опустить необходимости задавать их, если заключено в одинарные кавычки всю строку...

1. Значение конечной точки *гостиницы quickstart* коллекции документов и добавьте **поиска** параметр для передачи в строке запроса. 
  
   Эта строка выполняет пустым полем поиска (поиска = *), возвращения unranked списка (поиск оценка = 1.0) произвольных документов. По умолчанию службы поиска Azure возвращает 50 совпадений за раз. Как структурированных этот запрос возвращает структуру всего документа и значения. Добавить **$count = true** получить количество всех документов в результатах.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$count=true'
    ```

1. Выполните команду, чтобы отправить **$url** к службе.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Результаты должны выглядеть аналогично приведенному ниже.

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

Еще несколько примеров запросов к знакомству с синтаксисом. Вы можете строка поиска, запросов verbatim $filter, ограничить набор результатов, область поиска для конкретных полей и многое другое.

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up"></a>Очистка 

Следует удалить индекс, если он больше не нужен. Это бесплатная служба ограничена трех индексов. Вы можете удалить любые индексы, которые не используются активно, чтобы выполнять шаги другие руководства.

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06'

# Delete the index
Invoke-RestMethod -Uri $url -Headers $headers -Method Delete
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы использовали для пошагового выполнения базовый рабочий процесс для создания и доступа к содержимому в службе поиска Azure PowerShell. С основными понятиями, помните мы рекомендуем перейти к более сложные сценарии, такие как индексирование из источников данных Azure;

> [!div class="nextstepaction"]
> [Руководство по использованию REST: Индексирование и поиск частично структурированных данных (больших двоичных объектов JSON) в службе поиска Azure](search-semi-structured-data.md)