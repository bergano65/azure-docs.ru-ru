---
title: 'Быстрый запуск PowerShell: Создание, Загрузка и запрос индексов с помощью API-интерфейсов RESTFUL службы поиска Azure — Поиск Azure'
description: В этой статье объясняется, как создать индекс, загрузить данные и выполнить запросы с помощью командлета PowerShell Invoke-RestMethod и REST API поиска Azure.
ms.date: 07/11/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 6bff2c84a4bfd81b94054b85744c17a1cd217756
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67847064"
---
# <a name="quickstart-create-an-azure-search-index-in-powershell-using-rest-apis"></a>Краткое руководство. Создание индекса службы поиска Azure в PowerShell с помощью API-интерфейсов RESTFUL
> [!div class="op_single_selector"]
> * [PowerShell (ОСТАВШАЯся)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [POST (остальное)](search-get-started-postman.md)
> * [Python](search-get-started-python.md)
> * [Портал](search-create-index-portal.md)
> 

В этой статье описывается процесс создания, загрузки и запроса индекса службы поиска Azure с помощью PowerShell и [интерфейсов API службы "Поиск Azure](https://docs.microsoft.com/rest/api/searchservice/)". В этой статье объясняется, как выполнять команды PowerShell в интерактивном режиме. Кроме того, можно [скачать и запустить сценарий PowerShell](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) , выполняющий те же операции.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для этого краткого руководства необходимы следующие службы и средства. 

+ [PowerShell 5,1 или более поздней версии](https://github.com/PowerShell/PowerShell)с помощью [командлета Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) для последовательных и интерактивных шагов.

+ [Создайте службу "Поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. Вы можете использовать бесплатную службу для выполнения инструкций, описанных в этом кратком руководстве. 

## <a name="get-a-key-and-url"></a>Получение ключа и URL-адреса

Вызовам REST требуется URL-адрес службы и ключ доступа при каждом запросе. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **обзора** службы поиска получите URL-адрес. Пример конечной точки может выглядеть так: `https://mydemo.search.windows.net`.

2. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

![Получение конечной точки HTTP и ключа доступа](media/search-get-started-postman/get-url-key.png "Получение конечной точки HTTP и ключа доступа")

Для выполнения любого запроса к службе требуется использование ключа API. Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

## <a name="connect-to-azure-search"></a>Подключение к Поиску Azure

1. В PowerShell создайте объект **$headers** для хранения типа содержимого и ключа API. Замените ключ API администратора (ваш-ADMIN-API-KEY) ключом, который является допустимым для службы поиска. Этот заголовок необходимо задать только один раз в течение сеанса, но он будет добавлен в каждый запрос. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Создайте объект **$URL** , указывающий коллекцию индексов службы. Замените имя службы (My-SEARCH-SERVICE-NAME) допустимой службой поиска.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name"
    ```

3. Выполните **командлет Invoke-RestMethod** , чтобы отправить запрос GET в службу и проверить соединение. Добавьте **ConvertTo-JSON** , чтобы можно было просматривать ответы, отправленные из службы.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Если служба пуста и не имеет индексов, результаты будут похожи на приведенный ниже пример. В противном случае вы увидите представление определения индекса в формате JSON.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1\. Создание индекса

Если вы не используете портал, для загрузки данных должен существовать индекс в службе. Этот шаг определяет индекс и отправляет его в службу. Для этого шага используется [REST API создания индекса](https://docs.microsoft.com/rest/api/searchservice/create-index) .

Обязательные элементы индекса включают имя и коллекцию полей. Коллекция Fields определяет структуру *документа*. Каждое поле имеет имя, тип и атрибуты, определяющие его использование (например, возможность полнотекстового поиска, фильтрации или извлечения в результатах поиска). В индексе одно из полей типа `Edm.String` должно быть назначено в качестве *ключа* для удостоверения документа.

Этот индекс называется «Гостиницы-QuickStart» и содержит определения полей, которые вы видите ниже. Это подмножество более крупного [индекса гостиниц](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) , используемого в других пошаговых руководствах. Мы обрезать его в этом кратком руководстве для краткости.

1. Вставьте этот пример в PowerShell, чтобы создать объект **$Body** , содержащий схему индекса.

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

2. Задайте универсальный код ресурса (URI) для коллекции индексов в службе и в качестве индекса для *Гостиницы-QuickStart* .

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06"
    ```

3. Выполните команду с **$URL**, **$headers**и **$Body** , чтобы создать индекс в службе. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Результаты должны выглядеть следующим образом (усекаются до первых двух полей для краткости):

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
> Для проверки можно также проверить список индексов на портале.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2\. Загрузка документов

Чтобы отправить документы, используйте запрос HTTP POST к конечной точке URL-адреса индекса. REST API для этой задачи — [Добавление, обновление и удаление документов](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

1. Вставьте этот пример в PowerShell, чтобы создать объект **$Body** , содержащий документы, которые нужно передать. 

    Этот запрос включает две полные и частичные записи. Частичная запись показывает, что можно отправить неполные документы. Параметр `@search.action` указывает, как выполняется индексирование. Допустимые значения: upload, MERGE, mergeOrUpload и DELETE. Поведение mergeOrUpload либо создает новый документ для Хотелид = 3, либо обновляет содержимое, если оно уже существует.

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

1. Установите конечную точку в *коллекцию документов по* началу работы с документами и включите операцию с индексами (индексы или Гостиницы-краткое руководство, документацию и индекс).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06"
    ```

1. Выполните команду с **$URL**, **$headers**и **$Body** , чтобы загрузить документы в этот индекс для использования в гостиницах.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Результаты должны выглядеть примерно так, как показано в следующем примере. Должен отобразиться [код состояния 201](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

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

На этом шаге показано, как запросить индекс с помощью [API поиска документов](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Не забудьте использовать одинарные кавычки на $urls поиска. Строки запросов содержат **$** символы, и их можно опустить, если вся строка заключена в одинарные кавычки.

1. Задайте в качестве конечной точки коллекцию документов " *Гостиница — краткое руководство* " и добавьте параметр **поиска** для передачи строки запроса. 
  
   Эта строка выполняет пустой Поиск (Search = *), возвращая неупорядоченный список (оценка поиска = 1,0) для произвольных документов. По умолчанию поиск Azure возвращает 50 соответствий за раз. Как структурированный, этот запрос возвращает всю структуру и значения документа. Добавьте **$Count = true** , чтобы получить количество всех документов в результатах.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$count=true'
    ```

1. Выполните команду, чтобы отправить **$URL** в службу.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Результаты должны выглядеть примерно так, как показано ниже.

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

Попробуйте выполнить несколько других примеров запросов, чтобы получить представление о синтаксисе. Вы можете выполнять поиск по строкам, выполнять точные $filter запросов, ограничивать набор результатов, ограничить область поиска конкретными полями и многое другое.

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

Если вы работаете в своей подписке, по окончании проекта рекомендуем решить, нужны ли вам созданные ресурсы. Работающие ресурсы могут означать лишние затраты. Можно удалить отдельные ресурсы или удалить группу ресурсов, что позволит удалить весь набор ресурсов.

Просматривать ресурсы и управлять ими можно на портале с помощью ссылок **Все ресурсы** или **Группы ресурсов** на панели навигации слева.

При работе с бесплатной версией службы помните о том, что вам доступно максимум три индекса, индексатора и источника данных. Вы можете удалить отдельные элементы на портале, чтобы не превысить лимит. 

## <a name="next-steps"></a>Следующие шаги

В этом кратком руководстве вы использовали PowerShell для пошагового перехода на основной рабочий процесс создания и доступа к содержимому в службе поиска Azure. С учетом этих концепций рекомендуется переходить к более сложным сценариям, таким как индексирование из источников данных Azure.

> [!div class="nextstepaction"]
> [Руководство по работе с другими компонентами: Индексирование и поиск частично структурированных данных (BLOB-объекты JSON) в службе поиска Azure](search-semi-structured-data.md)