---
title: Краткое руководство. Python и API-интерфейсы REST - службы поиска Azure
description: Создание, загрузка и отправка запросов в индекс, с помощью Python, записные книжки Jupyter и API REST службы поиска Azure.
ms.date: 05/15/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 1ab6bb069f60f4d2dbb4cfaecda54c3c2ef20adc
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806436"
---
# <a name="quickstart-create-an-azure-search-index-using-jupyter-python-notebooks"></a>Краткое руководство. Создание индекса службы поиска Azure с помощью записных книжек Jupyter Python
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Портал](search-create-index-portal.md)
> 

Создание записной книжки Jupyter, который создает, загружает и отправляет запрос поиска Azure [индекс](search-what-is-an-index.md) с помощью Python и [интерфейсы API REST службы поиска Azure](https://docs.microsoft.com/rest/api/searchservice/). В этой статье объясняется, как создавать собственные записной книжки шаг за шагом. При необходимости можно запустить готовое записной книжки. Чтобы загрузить копию, перейдите к [репозитория Azure-Search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples).

Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) перед началом, а затем [зарегистрируйтесь в службе "Поиск Azure"](search-create-service-portal.md).

## <a name="prerequisites"></a>Технические условия

В этом кратком руководстве используются перечисленные ниже службы и инструменты. 

+ [Создайте службу "Поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. Вы можете использовать бесплатную службу для выполнения инструкций, описанных в этом кратком руководстве. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), предоставляя Python 3.x и записные книжки Jupyter.

## <a name="get-a-key-and-url"></a>Получение ключа и URL-адреса

Вызовам REST требуется URL-адрес службы и ключ доступа при каждом запросе. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **обзора** службы поиска получите URL-адрес. Пример конечной точки может выглядеть так: `https://mydemo.search.windows.net`.

1. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

![Получение конечной точки HTTP и ключа доступа](media/search-fiddler/get-url-key.png "Получение конечной точки HTTP и ключа доступа")

Для выполнения любого запроса к службе требуется использование ключа API. Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

## <a name="connect-to-azure-search"></a>Подключение к Поиску Azure

Откройте записную книжку Jupyter и проверить подключение из локальной рабочей станции, запросив список индексов в службе. В Windows с помощью Anaconda3 можно использовать Навигатор Anaconda запустить записную книжку.

1. Создание записной книжки Python3.

1. В первой ячейке Загрузите библиотеки, которые используются для работы с JSON и составления HTTP-запросов.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. Во второй ячейке входные элементы запроса, которые будут константы при каждом запросе. Замените имя службы поиска (YOUR-SEARCH-SERVICE-NAME) и ключ API администратора (YOUR-ADMIN-API-KEY) значениями, действительными. 

   ```python
    endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
    api_version = '?api-version=2019-05-06'
    headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. В третью ячейку сформулируйте запрос. Этот запрос GET обращается к коллекции индексов службы поиска и выбирает свойства name.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Выполните каждый шаг. Если существует индексов, ответ содержит список индексов. На следующем снимке экрана служба включает в себя azureblob индекса и индекса realestate-us-sample.

   ![Скрипт Python в записной книжке Jupyter с HTTP запросы в службу поиска Azure](media/search-get-started-python/connect-azure-search.png "скрипт Python в записной книжке Jupyter с HTTP запросы в службу поиска Azure")

   Коллекцию индексов пустой возвращает такой ответ: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

> [!Tip]
> На это бесплатная служба вы будете ограничены трех индексов, индексаторов и источников данных. В этом кратком руководстве создается по одному экземпляру каждого. Убедитесь, что у вас достаточно места для создания новых объектов, прежде чем продолжить.

## <a name="1---create-an-index"></a>1. Создание индекса

Если вы используете портал, индекс должен существовать в службе перед загрузкой данных. Этот шаг использует [REST API создания индекса](https://docs.microsoft.com/rest/api/searchservice/create-index) для принудительной отправки схему индекса для службы

Коллекция полей определяет структуру *документа*. Обязательные элементы индекса включают имя и коллекцию полей. Каждое поле имеет имя, тип и атрибуты, которые определяют, как он используется (например, является ли это полнотекстового поиска для поиска, фильтрацию или отображалось в результатах поиска). В индексе, одно из полей типа `Edm.String` необходимо назначить в качестве *ключ* для идентификации документа.

Этот индекс имеет имя «hotels-py» и определения полей, указанные ниже. Он является частью более крупной [индекса отелей](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) используется в других пошаговых руководств. Мы усекаются в этом кратком руководстве для краткости.


1. В следующей ячейке вставьте следующий пример в ячейку, чтобы указать схему. 

    ```python
    index_schema = {
       "name": "hotels-py",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. В другую ячейку сформулируйте запрос. Этого ПОМЕСТИТЕ запрос обращается к коллекции индексов службы поиска и создается индекс на основе схемы индекс, указанный на предыдущем шаге.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Выполните каждый шаг.

   Ответ включает JSON-представление схемы. Следующий снимок экрана Обрезка части схемы индекса, таким образом, чтобы вы могли увидеть больше ответа.

    ![Запрос на создание индекса](media/search-get-started-python/create-index.png "запрос на создание индекса")

> [!Tip]
> Для проверки подлинности, можно также проверить список индексов на портале или повторно выполнить запрос на подключение службы к см. в разделе *гостиницы py* индекса, перечисленных в коллекции индексов.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2. Загрузка документов

Чтобы отправить документы, используйте запрос HTTP POST к конечной точке URL-адрес вашего индекса. REST API — [Добавление, обновление и удаление документов](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Документы создаются из [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) на сайте GitHub.

1. В новую ячейку предоставляют три документа, которые соответствуют схеме индекса. Укажите действие отправки для каждого документа.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
        }
      }
     ]
    }
    ```

2. В другую ячейку сформулируйте запрос. Для этого запроса POST обращается к коллекции документация индекса отелей py и помещает документы, предоставляемые на предыдущем шаге.

   ```python
   url = endpoint + "indexes/hotels-py/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Выполните каждый шаг, чтобы отправлять документы в индекс службы поиска. Результаты должны выглядеть аналогично приведенному ниже. 

   ```
   {'@odata.context': "https://mydemo.search.windows.net/indexes('hotels-py')/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
    'value': [{'errorMessage': None,
            'key': '1',
            'status': True,
            'statusCode': 201},
           {'errorMessage': None,
            'key': '2',
            'status': True,
            'statusCode': 201},
           {'errorMessage': None,
            'key': '3',
            'status': True,
            'statusCode': 201}]}
     ```


## <a name="3---search-an-index"></a>3. Поиск в индексе

Этот шаг показывает, как запросить индекс с помощью [REST API службы поиска документов](https://docs.microsoft.com/rest/api/searchservice/search-documents).


1. В новую ячейку Укажите выражение запроса. Следующий пример выполняет поиск терминов «hotels» и «wifi». Он также возвращает *число* документов, которые совпадают, и *выбирает* полей, включаемых в результатах поиска.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

2. Сформулируйте запрос. Этот запрос GET обращается к коллекции документация индекса отелей py и присоединяет запрос, который вы указали на предыдущем шаге.

   ```python
   url = endpoint + "indexes/hotels-py/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

   Результаты должны выглядеть аналогично приведенному ниже.

   ```
   {'@odata.context': "https://mydemo.search.windows.net/indexes('hotels-py')/$metadata#docs(*)",
    '@odata.count': 3,
    'value': [{'@search.score': 1.0,
               'HotelId': '1',
               'HotelName': 'Secret Point Motel'},
              {'@search.score': 1.0,
               'HotelId': '2',
               'HotelName': 'Twin Dome Motel'},
              {'@search.score': 1.0,
               'HotelId': '3',
               'HotelName': 'Triple Landscape Hotel'}]}
   ```

3. Еще несколько примеров запросов к знакомству с синтаксисом. Вы можете применить фильтр, занять два первых результата, упорядочить по определенному полю, или 

   + `searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description'`

   + `searchstring = '&search=hotel&$top=2&$select=HotelId,HotelName,Description'`

   + `searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince'`

## <a name="clean-up"></a>Очистка 

Следует удалить индекс, если он больше не нужен. Это бесплатная служба ограничена трех индексов. Вы можете удалить все индексы, которые активно не используется, чтобы освободить место для других учебников.

   ```python
  url = endpoint + "indexes/hotels-py" + api_version
  response  = requests.delete(url, headers=headers)
   ```

Удаление индекса можно проверить, возвращая список существующих индексов. Если отсутствует py гостиницы, то знаете, ваш запрос выполнен успешно.

```python
url = endpoint + "indexes" + api_version + "&$select=name"

response  = requests.get(url, headers=headers)
index_list = response.json()
pprint(index_list)
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о сценариях и синтаксис запроса.

> [!div class="nextstepaction"]
> [Создание базового запроса](search-query-overview.md)
