---
title: Краткое руководство. Python и API-интерфейсы REST - службы поиска Azure
description: Создание, загрузка и отправка запросов в индекс, с помощью Python, записные книжки Jupyter и API REST службы поиска Azure.
ms.date: 06/11/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: c519cbd151ac3008593e3309930db4e9a9414e51
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056641"
---
# <a name="quickstart-create-an-azure-search-index-using-jupyter-python-notebooks"></a>Краткое руководство. Создание индекса службы поиска Azure с помощью записных книжек Jupyter Python
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Портал](search-create-index-portal.md)
> 

Создание записной книжки Jupyter, который создает, загружает и отправляет запрос в индекс поиска Azure с помощью Python и [API REST службы поиска Azure](https://docs.microsoft.com/rest/api/searchservice/). В этой статье объясняется, как создать записную книжку шаг за шагом, начиная с нуля. Кроме того можно запустить готовой записной книжки. Чтобы загрузить копию, перейдите к [репозитория Azure-Search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

В этом кратком руководстве используются перечисленные ниже службы и инструменты. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), предоставляя Python 3.x и записные книжки Jupyter.

+ [Создайте службу "Поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. В этом кратком руководстве используется уровень "бесплатный". 

## <a name="get-a-key-and-url"></a>Получение ключа и URL-адреса

Вызовам REST требуется URL-адрес службы и ключ доступа при каждом запросе. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **обзора** службы поиска получите URL-адрес. Пример конечной точки может выглядеть так: `https://mydemo.search.windows.net`.

1. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

![Получение конечной точки HTTP и ключа доступа](media/search-fiddler/get-url-key.png "Получение конечной точки HTTP и ключа доступа")

Для выполнения любого запроса к службе требуется использование ключа API. Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

## <a name="connect-to-azure-search"></a>Подключение к Поиску Azure

В этой задаче запустить записную книжку Jupyter и убедитесь, что можно подключиться к службе поиска Azure. Это будет сделать, запросив список индексов из службы. В Windows с помощью Anaconda3 можно использовать Навигатор Anaconda запустить записную книжку.

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

1. В третью ячейку сформулируйте запрос. Этот запрос GET обращается к коллекции индексов службы поиска и выбирает свойства name атрибута существующие индексы.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Выполните каждый шаг. Если существует индексов, ответ содержит список имен индексов. На следующем снимке экрана служба уже имеет azureblob индекса и индекса realestate-us-sample.

   ![Скрипт Python в записной книжке Jupyter с HTTP запросы в службу поиска Azure](media/search-get-started-python/connect-azure-search.png "скрипт Python в записной книжке Jupyter с HTTP запросы в службу поиска Azure")

   Напротив коллекции пустой индекс возвращает такой ответ: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1\. Создание индекса

Если вы используете портал, индекс должен существовать в службе перед загрузкой данных. Этот шаг использует [REST API создания индекса](https://docs.microsoft.com/rest/api/searchservice/create-index) для принудительной отправки схему индекса для службы.

Обязательные элементы индекса включают имя, коллекцию полей и ключ. Коллекция полей определяет структуру *документа*. Каждое поле имеет имя, тип и атрибуты, которые определяют, как используется поле (например, является ли это полнотекстового поиска для поиска, фильтрацию или отображалось в результатах поиска). В индексе, одно из полей типа `Edm.String` необходимо назначить в качестве *ключ* для идентификации документа.

Этот индекс имеет имя «hotels-quickstart» и определения полей, указанные ниже. Он является частью более крупной [индекса отелей](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) используется в других пошаговых руководств. Мы усекаются в этом кратком руководстве для краткости.

1. В следующей ячейке вставьте следующий пример в ячейку, чтобы указать схему. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
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

2. В другую ячейку сформулируйте запрос. Этого ПОМЕСТИТЕ запрос обращается к коллекции индексов службы поиска и создается индекс на основе схемы индекс, указанный в предыдущей ячейке.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Выполните каждый шаг.

   Ответ включает JSON-представление схемы. Следующий снимок экрана отображается только часть ответа.

    ![Запрос на создание индекса](media/search-get-started-python/create-index.png "запрос на создание индекса")

> [!Tip]
> Другой способ проверить создание индекса — проверить список индексов на портале.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2\. Загрузка документов

Чтобы отправить документы, используйте запрос HTTP POST к конечной точке URL-адрес вашего индекса. REST API — [Добавление, обновление и удаление документов](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Документы создаются из [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) на сайте GitHub.

1. В новую ячейку предоставляют четыре документа, которые соответствуют схеме индекса. Укажите действие отправки для каждого документа.

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
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```   

2. В другую ячейку сформулируйте запрос. Для этого запроса POST обращается к коллекции документация индекса отелей quickstart и помещает документы, предоставляемые на предыдущем шаге.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Выполните каждый шаг, чтобы отправлять документы в индекс службы поиска. Результаты должны выглядеть аналогично приведенному ниже. 

    ![Отправка документов в индекс](media/search-get-started-python/load-index.png "отправлять документы в индекс")

## <a name="3---search-an-index"></a>3\. Поиск в индексе

Этот шаг показывает, как запросить индекс с помощью [REST API службы поиска документов](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. В ячейке, укажите выражение запроса, которое выполняет пустым полем поиска (search = *), возвращения unranked списка (поиск оценка = 1.0) произвольных документов. По умолчанию службы поиска Azure возвращает 50 совпадений за раз. Как структурированных этот запрос возвращает структуру всего документа и значения. Добавление $count = true, чтобы получить количество всех документов в результатах.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. В новую ячейку укажите приведенный ниже, для поиска терминов «hotels» и «wifi». Добавьте $select для указания полей, включаемых в результатах поиска.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. В другую ячейку формулировки запроса. Этот запрос GET обращается к коллекции документация индекса отелей quickstart и присоединяет запрос, который вы указали на предыдущем шаге.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Выполните каждый шаг. Результаты должны выглядеть аналогично приведенному ниже. 

    ![Поиск в индексе](media/search-get-started-python/search-index.png "поиск в индексе")

1. Еще несколько примеров запросов к знакомству с синтаксисом. Можно заменить фрагментом searchstring с приведенными ниже примерами и затем снова запустите запрос поиска. 

   Применение фильтра: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Выполните два лучших результатов.

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Упорядочить по определенному полю:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>Очистка 

Следует удалить индекс, если он больше не нужен. Это бесплатная служба ограничена трех индексов. Следует удалить все индексы, которые активно не используется, чтобы освободить место для других учебников.

Самый простой способ удалить объекты, — через портал, но так как это краткое руководство Python, следующий синтаксис такой же результат:

   ```python
  url = endpoint + "indexes/hotels-quickstart" + api_version
  response  = requests.delete(url, headers=headers)
   ```

Удаление индекса можно проверить, запросив список существующих индексов. Если отсутствует гостиницы quickstart, то знаете, ваш запрос выполнен успешно.

```python
url = endpoint + "indexes" + api_version + "&$select=name"

response  = requests.get(url, headers=headers)
index_list = response.json()
pprint(index_list)
```

## <a name="next-steps"></a>Дальнейшие действия

Упрощения в этом кратком руководстве используется сокращенная версия индекса отелей. Можно создать полную версию Пробный запрос более интересным. Чтобы получить полную версию и все документы, 50, выполните **импорта данных** мастера, выбрав *гостиницы sample* из источников данных встроенного образца.

> [!div class="nextstepaction"]
> [Краткое руководство Создание индекса на портале Azure](search-get-started-portal.md)
