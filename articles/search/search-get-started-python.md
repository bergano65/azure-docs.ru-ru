---
title: Краткое руководство. Создание индекса поиска в Python с помощью интерфейсов REST API
titleSuffix: Azure Cognitive Search
description: Описание процессов создания индекса, загрузки данных и выполнения запросов с помощью Python, Jupyter Notebook и REST API службы "Когнитивный поиск Azure".
author: heidisteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/04/2019
ms.openlocfilehash: c663fae47de1e161314aa3bf2fdb9966ae80d3c6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792263"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>Краткое руководство. Создание индекса службы "Когнитивный поиск Azure" в Python с помощью записных книжек Jupyter

> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-get-started-postman.md)
> * [Портал](search-create-index-portal.md)
> 

Создайте записную книжку Jupyter, которая создает, загружает и запрашивает индекс службы "Когнитивный поиск Azure", с помощью Python и [REST API службы "Когнитивный поиск Azure"](https://docs.microsoft.com/rest/api/searchservice/). В этой статье содержатся сведения о пошаговом создании записной книжки. Вы также можете [скачать и запустить готовую записную книжку Jupyter Python](https://github.com/Azure-Samples/azure-search-python-samples).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения инструкций из этого краткого руководства необходимы перечисленные ниже службы и инструменты. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section) с Python 3.x и записными книжками Jupyter Notebook.

+ [Создайте службу "Когнитивный поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. Для выполнения инструкций, описанных в этом кратком руководстве, можно использовать уровень "Бесплатный". 

## <a name="get-a-key-and-url"></a>Получение ключа и URL-адреса

Вызовам REST требуется URL-адрес службы и ключ доступа при каждом запросе. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Когнитивный поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **обзора** службы поиска получите URL-адрес. Пример конечной точки может выглядеть так: `https://mydemo.search.windows.net`.

1. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

![Получение конечной точки HTTP и ключа доступа](media/search-get-started-postman/get-url-key.png "Получение конечной точки HTTP и ключа доступа")

Для выполнения любого запроса к службе требуется использование ключа API. Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

## <a name="connect-to-azure-cognitive-search"></a>Подключение к Когнитивному поиску Azure

Для выполнения этой задачи запустите записную книжку Jupyter и проверьте, можете ли вы подключиться к службе "Когнитивный поиск Azure". Для этого запросите список индексов из службы. В Windows с Anaconda3 для запуска записной книжки можно использовать Anaconda Navigator.

1. Создайте записную книжку Python3.

1. В первой ячейке загрузите библиотеки, используемые для работы с JSON и формулировки HTTP-запросов.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. Во второй ячейке введите элементы запроса, которые будут выполнять роль констант при каждом запросе. Замените заполнители имени службы поиска (YOUR-SEARCH-SERVICE-NAME) и ключа API администрирования (YOUR-ADMIN-API-KEY) реальными значениями. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   Если вы получаете ошибку подключения `"Failed to establish a new connection"`, убедитесь, что ключ API является первичным или вторичным ключом администратора и присутствуют все начальные и конечные символы (`?` и `/`).

1. В третьей ячейке сформулируйте сам запрос. Этот запрос GET отправляется в коллекцию индексов службы поиска и выбирает свойство имен существующих индексов.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Выполните каждый шаг. Если индексы существуют, в ответе будет содержаться список имен индексов. На следующем снимке экрана служба уже содержит индексы azureblob-index и realestate-bs-sample.

   ![Скрипт Python в записной книжке Jupyter с HTTP-запросами к службе "Когнитивный поиск Azure"](media/search-get-started-python/connect-azure-search.png "Скрипт Python в записной книжке Jupyter с HTTP-запросами к службе "Когнитивный поиск Azure"")

   Пустая коллекция индексов возвращает следующий ответ: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1\. Создание индекса

Если вы не используете портал, для загрузки данных необходимо наличие индекса в службе. На этом шаге для отправки схемы индекса в службу используется [REST API создания индекса](https://docs.microsoft.com/rest/api/searchservice/create-index).

Обязательные элементы индекса включают в себя имя, коллекцию полей и ключ. Коллекция полей определяет структуру *документа*. Каждое поле имеет имя, тип и атрибуты, которые определяют использование поля (например, обладает ли оно возможностью полнотекстового поиска, можно ли его отфильтровать или извлечь из результатов поиска). В индексе одно из полей типа `Edm.String` должно быть назначено в качестве *ключа* для идентификации документа.

Этот индекс называется hotels-quickstart и содержит приведенные ниже определения полей. Это подмножество более крупного [индекса Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON), используемого в рамках других пошаговых руководств. В этом руководстве мы его сократили.

1. В следующую ячейку вставьте приведенный ниже пример для предоставления схемы. 

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

2. В другой ячейке сформулируйте сам запрос. Этот запрос PUT отправляется в коллекцию индексов службы поиска и создает индекс на основе схемы индекса, которую вы указали в предыдущей ячейке.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Выполните каждый шаг.

   Ответ будет содержать JSON-представление схемы. На следующем снимке экрана показана только часть ответа.

    ![Запрос на создание индекса](media/search-get-started-python/create-index.png "Запрос на создание индекса")

> [!Tip]
> Другой способ проверки создания индекса — проверка списка индексов на портале.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2\. Загрузка документов

Чтобы передать документы, отправьте запрос HTTP POST в конечную точку URL-адреса индекса. Используется REST API [добавления, обновления и удаления документов](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Источником документов является [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) на сайте GitHub.

1. В новой ячейке укажите четыре документа, соответствующие схеме индекса. Укажите действие отправки для каждого документа.

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

2. В другой ячейке сформулируйте сам запрос. Этот запрос POST поступает в коллекцию документов индекса hotels-quickstart и отправляет документы, предоставленные на предыдущем шаге.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Выполните каждый шаг, чтобы отправить документы в индекс в службе поиска. Результаты должны выглядеть следующим образом. 

    ![Отправка документов в индекс](media/search-get-started-python/load-index.png "Отправка документов в индекс")

## <a name="3---search-an-index"></a>3\. Поиск в индексе

В рамках этого раздела мы покажем, как создать запрос в индекс с помощью [REST API поиска документов](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. В ячейке укажите выражение запроса, который выполняет пустой поиск (search=*), возвращая неупорядоченный список (search score = 1.0) произвольных документов. По умолчанию служба "Когнитивный поиск Azure" возвращает 50 лучших соответствий за раз. Как структурированный этот запрос возвращает структуру и значения документа. Добавьте $count=true, чтобы получить количество всех документов в результатах.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. В новой ячейке укажите следующий пример для поиска по терминам hotels и wifi. Добавьте $select, чтобы указать, какие поля следует включить в результаты поиска.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. В другой ячейке сформулируйте запрос. Этот запрос GET поступает в коллекцию документов индекса hotels-quickstart и присоединяет запрос, указанный на предыдущем шаге.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Выполните каждый шаг. Результаты должны выглядеть следующим образом. 

    ![Поиск в индексе](media/search-get-started-python/search-index.png "Поиск в индексе")

1. Выполните еще несколько примеров запросов, чтобы ознакомиться с синтаксисом. Вы можете заменить `searchstring` приведенными ниже примерами, а затем повторно выполнить запрос на поиск. 

   Примените фильтр: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Возьмите два лучших результата:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Упорядочите по определенному полю:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>Очистка

Если вы работаете в своей подписке, по окончании проекта рекомендуем решить, нужны ли вам созданные ресурсы. Работающие ресурсы могут означать лишние затраты. Можно удалить отдельные ресурсы или удалить группу ресурсов, что позволит удалить весь набор ресурсов.

Просматривать ресурсы и управлять ими можно на портале с помощью ссылок **Все ресурсы** или **Группы ресурсов** на панели навигации слева.

При работе с бесплатной версией службы помните о том, что вам доступно максимум три индекса, индексатора и источника данных. Вы можете удалить отдельные элементы на портале, чтобы не превысить лимит. 

## <a name="next-steps"></a>Дополнительная информация

Для упрощения в этом кратком руководстве используется сокращенная версия индекса Hotels. Вы можете создать полную версию, чтобы выполнить более интересные запросы. Чтобы получить полную версию и все 50 документов, запустите мастер **импорта данных**, выбрав *hotels-sample* из встроенных примеров источников данных.

> [!div class="nextstepaction"]
> [Краткое руководство Создание индекса службы "Поиск Azure" с помощью портала Azure](search-get-started-portal.md)
