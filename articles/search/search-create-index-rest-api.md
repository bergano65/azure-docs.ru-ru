---
title: Создание индекса в коде с помощью PowerShell и API REST - службы поиска Azure
description: Сведения о создании полнотекстового индекса с поддержкой поиска в коде с помощью запросов HTTP и REST API службы "Поиск Azure".
ms.date: 03/15/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 87da5cdd31abb41a774a46d3891006eb58ac5e4d
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285141"
---
# <a name="quickstart-create-an-azure-search-index-using-powershell-and-the-rest-api"></a>Краткое руководство. Создание индекса службы поиска Azure с помощью PowerShell и REST API
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Портал](search-create-index-portal.md)
> 

В этой статье описывается процесс создания, загрузки и запроса поиска Azure [индекс](search-what-is-an-index.md) с помощью PowerShell и [API REST службы поиска Azure](https://docs.microsoft.com/rest/api/searchservice/). Определения индекса и содержимое для поиска предоставляется в тексте запроса как JSON-содержимого правильного формата.

## <a name="prerequisites"></a>Технические условия

[Создание службы поиска Azure](search-create-service-portal.md) или [найти существующую службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. Можно использовать это бесплатная служба, в этом кратком руководстве. Другие предварительные требования включают следующие элементы.

[PowerShell 5.1 или более поздней версии](https://github.com/PowerShell/PowerShell), с использованием [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) для последовательных и интерактивных шагов.

URL-адрес конечной точки и администратора ключ api службы поиска. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. На портале Azure, в службе поиска **Обзор** странице, получить URL-адрес. Пример конечной точки может выглядеть https:\//my-service-name.search.windows.net.

2. В **параметры** > **ключи**, получите ключ администратора для полных прав в службе. Существует два ключа администратора взаимозаменяемыми, предоставленный для обеспечения непрерывности бизнеса, при необходимости продления, один. Для добавления, изменения и удаления объектов, можно использовать либо первичный или вторичный ключ на запросы.

   ![Получение ключа конечной точки и доступ HTTP](media/search-fiddler/get-url-key.png "получить HTTP конечной точки и ключа доступа")

   Все запросы, требуют ключ api на каждый запрос, отправленный к вашей службе. Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

## <a name="connect-to-azure-search"></a>Подключение к поиску Azure

В PowerShell, создать **$headers** объект для сохранения content-type и ключ API. Необходимо присвоить этому заголовку один раз в течение сеанса, но необходимо добавить в каждый запрос. 

```powershell
$headers = @{
   'api-key' = '<your-admin-api-key>'
   'Content-Type' = 'application/json' 
   'Accept' = 'application/json' }
```

Создание **$url** , указывающий службу индексов коллекции. `mydemo` Имя службы предназначен в качестве заполнителя. Замените его допустимым поиска службы в текущей подписке, в этом примере.

```powershell
$url = "https://mydemo.search.windows.net/indexes?api-version=2017-11-11"
```

Запустите **Invoke-RestMethod** отправить запрос GET в службу и проверьте подключение. Добавить **ConvertTo-Json** , и вы сможете просматривать ответы, отправляемые службой.

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

## <a name="1---create-an-index"></a>1. Создание индекса

Если вы используете портал, индекс должен существовать в службе перед загрузкой данных. Этот шаг определяет индекс и помещает его в службу. [Create Index (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) используется для выполнения этого шага.

Обязательные элементы индекса включают имя и коллекцию полей. Коллекция полей определяет структуру *документа*. Каждое поле имеет имя, тип и атрибуты, которые определяют, как он используется (например, является ли это полнотекстового поиска для поиска, фильтрацию или отображалось в результатах поиска). В индексе, одно из полей типа `Edm.String` необходимо назначить в качестве *ключ* для идентификации документа.

Этот индекс имеет имя «hotels» и определения полей, указанные ниже. Указывает определение индекса [анализатор языка](index-add-language-analyzers.md) для `description_fr` поле, так как оно предназначено для текста на французском языке, который мы добавим в примере ниже.

Вставьте этот пример в PowerShell для создания **$body** объект, содержащий схему индекса.

```powershell
$body = @"
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
"@
```

Задать URI для коллекции индексов в службе и *гостиницы* индекса.

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11"
```

Выполните команду с **$url**, **$headers**, и **$body** для создания индекса в службе. 

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
```
Результаты должны выглядеть примерно так (усеченный в первые два поля, для краткости):

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag":  "\"0x8D6A99E2DED96B0\"",
    "name":  "hotels",
    "defaultScoringProfile":  null,
    "fields":  [
                   {
                       "name":  "hotelId",
                       "type":  "Edm.String",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  false,
                       "facetable":  false,
                       "key":  true,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
                   {
                       "name":  "baseRate",
                       "type":  "Edm.Double",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  true,
                       "facetable":  true,
                       "key":  false,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
. . .
```

> [!Tip]
> Для проверки подлинности, можно также проверить список индексов на портале или повторно выполнить команду, используемый для проверки подключения к службе, см. в разделе *гостиницы* индекса, перечисленных в коллекции индексов.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Загрузка документов

Чтобы отправить документы, используйте запрос HTTP POST к конечной точке URL-адрес вашего индекса. REST API для выполнения этой задачи — [Добавление, обновление и удаление документов](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

Вставьте этот пример в PowerShell для создания **$body** объект, содержащий документы, который необходимо передать. 

Этот запрос содержит две полные резервные копии и частичные записи. Частичные записи показано, вы можете отправить документы неполной. `@search.action` Параметр указывает, как выполняется индексирование. Допустимые значения: отправка, merge, mergeOrUpload и delete. Поведение mergeOrUpload либо создает новый документ для hotelId = 3 или обновляет содержимое, если он уже существует.

```powershell
$body = @"
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        }
    ]
}
"@
```

Значение конечной точки *гостиницы* коллекции документов и включить операции с индексом (индексы/hotels/документы/index).

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11"
```

Выполните команду с **$url**, **$headers**, и **$body** для загрузки документов в индекс отелей.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
```
Результаты должны выглядеть аналогично приведенному ниже. Вы увидите код состояния 201. Описание всех кодов состояния, см. в разделе [коды состояния HTTP (поиск Azure)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2017_11_11.IndexResult)",
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
                  }
              ]
}
```

## <a name="3---search-an-index"></a>3. Поиск в индексе

Этот шаг показывает, как запросить индекс с помощью [API поиска документов](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Значение конечной точки *гостиницы* коллекции документов и добавьте **поиска** параметр для включения строк запроса. Эта строка является пустым полем поиска и возвращает unranked список всех документов.

```powershell
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*'
```

Выполните команду, чтобы отправить **$url** к службе.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

Результаты должны выглядеть аналогично приведенному ниже.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#docs(*)",
    "value":  [
                  {
                      "@search.score":  1.0,
                      "hotelId":  "1",
                      "baseRate":  199.0,
                      "description":  "Best hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Fancy Stay",
                      "category":  "Luxury",
                      "tags":  "pool view wifi concierge",
                      "parkingIncluded":  false,
                      "smokingAllowed":  false,
                      "lastRenovationDate":  "2010-06-27T00:00:00Z",
                      "rating":  5,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "2",
                      "baseRate":  79.99,
                      "description":  "Cheapest hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Roach Motel",
                      "category":  "Budget",
                      "tags":  "motel budget",
                      "parkingIncluded":  true,
                      "smokingAllowed":  true,
                      "lastRenovationDate":  "1982-04-28T00:00:00Z",
                      "rating":  1,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "3",
                      "baseRate":  129.99,
                      "description":  "Close to town hall and the river",
                      "description_fr":  null,
                      "hotelName":  null,
                      "category":  null,
                      "tags":  "",
                      "parkingIncluded":  null,
                      "smokingAllowed":  null,
                      "lastRenovationDate":  null,
                      "rating":  null,
                      "location":  null
                  }
              ]
}
```

Еще несколько примеров запросов к знакомству с синтаксисом. Вы можете строка поиска, запросов verbatim $filter, ограничить набор результатов, область поиска для конкретных полей и многое другое.

```powershell
# Query example 1
# Search the entire index for the term 'budget'
# Return only the `hotelName` field, "Roach hotel"
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=budget&$select=hotelName'

# Query example 2 
# Apply a filter to the index to find hotels cheaper than $150 per night
# Returns the `hotelId` and `description`. Two documents match.
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$filter=baseRate lt 150&$select=hotelId,description'

# Query example 3
# Search the entire index, order by a specific field (`lastRenovationDate`) in descending order
# Take the top two results, and show only `hotelName` and `lastRenovationDate`
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate'
```
## <a name="clean-up"></a>Очистка 

Следует удалить индекс, если он больше не нужен. Это бесплатная служба ограничена трех индексов. Вы можете удалить любые индексы, которые не используются активно, чтобы выполнять шаги другие руководства.

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11'

# Delete the index
Invoke-RestMethod -Uri $url -Headers $headers -Method Delete
```

## <a name="next-steps"></a>Дальнейшие действия

Попробуйте добавить французский описания в индекс. В следующем примере включает в себя строки на французском языке и показаны дополнительные действия. Используйте mergeOrUpload для создания или добавления существующих полей. Следующие строки должны быть в кодировке UTF-8.

```json
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "1",
            "description_fr": "Meilleur hôtel en ville"
        },
        {
            "@search.action": "merge",
            "hotelId": "2",
            "description_fr": "Hôtel le moins cher en ville",
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```
