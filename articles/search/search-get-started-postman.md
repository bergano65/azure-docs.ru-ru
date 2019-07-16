---
title: Краткое руководство. Создание, загрузка и запрос индексов с помощью Postman и REST API в службе "Поиск Azure"
description: Из этой статьи вы узнаете, как вызывать REST API службы "Поиск Azure" с помощью Postman, определений и примера данных.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 07/09/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: ee3c4da96629910801413cbbad69963defb87dfe
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798782"
---
# <a name="quickstart-create-an-azure-search-index-in-postman-using-rest-apis"></a>Краткое руководство. Создание индекса службы "Поиск Azure" в Postman с помощью REST API
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Портал](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

[REST API службы "Поиск Azure"](https://docs.microsoft.com/rest/api/searchservice) проще всего изучить с помощью Postman или другого средства веб-тестирования для формирования HTTP-запросов и изучения ответов. С помощью соответствующих средств и этих инструкций вы сможете отправлять запросы и просматривать ответы без написания кода.

Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) перед началом, а затем [зарегистрируйтесь в службе "Поиск Azure"](search-create-service-portal.md).

## <a name="prerequisites"></a>Предварительные требования

В этом кратком руководстве используются перечисленные ниже службы и инструменты. 

+ [Создайте службу "Поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. Вы можете использовать бесплатную службу для выполнения инструкций, описанных в этом кратком руководстве. 

+ Используйте [классическое приложение Postman](https://www.getpostman.com/) для отправки запросов в службу "Поиск Azure".

## <a name="get-a-key-and-url"></a>Получение ключа и URL-адреса

Вызовам REST требуется URL-адрес службы и ключ доступа при каждом запросе. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **обзора** службы поиска получите URL-адрес. Пример конечной точки может выглядеть так: `https://mydemo.search.windows.net`.

1. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

![Получение конечной точки HTTP и ключа доступа](media/search-get-started-postman/get-url-key.png "Получение конечной точки HTTP и ключа доступа")

Для выполнения любого запроса к службе требуется использование ключа API. Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

## <a name="connect-to-azure-search"></a>Подключение к Поиску Azure

В этом разделе вы примените удобные для вас веб-инструменты, чтобы настроить подключения к Поиску Azure. Каждый инструмент сохраняет сведения заголовка запроса для сеанса. Это означает, что ключ API и тип содержимого нужно ввести только один раз.

Независимо от выбранного средства, вам нужно выбрать команду (GET, POST, PUT и т. д.), предоставить URL-адрес конечной точки, а для некоторых задач — включить код JSON в текст запроса. Замените имя службы поиска (YOUR-SEARCH-SERVICE-NAME) допустимым значением. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06

Запишите префикс HTTPS, имя службы, имя объекта (в данном случае коллекцию индексов) и значение [api-version](search-api-versions.md). Версия API (api-version) является обязательной — это строка в нижнем регистре, заданная как `?api-version=2019-05-06` для текущей версии. Версии API регулярно обновляются. Включая версию API для каждого запроса дает полный контроль над используемой версией.  

Структура заголовка запроса содержит два элемента: тип содержимого (Content-Type) и ключ API (api-key) для аутентификации в службе "Поиск Azure". Замените ключ API администратора (YOUR-ADMIN-API-KEY) допустимым значением. 

    api-key: <YOUR-ADMIN-API-KEY>
    Content-Type: application/json

Сформулируйте в Postman запрос такого формата, как показано на следующем снимке экрана. Выберите команду **GET**, укажите URL-адрес и нажмите **Отправить**. Эта команда подключается к Поиску Azure, считывает коллекцию индексов и возвращает код состояния HTTP 200 при успешном подключении. Если служба уже имеет индексы, ответ также будет содержать определения индекса.

![Заголовок запроса Postman][6]

## <a name="1---create-an-index"></a>1\. Создание индекса

В Поиске Azure индекс обычно создается заранее, до его загрузки вместе с данными. Для этой цели используется [REST API для создания индекса](https://docs.microsoft.com/rest/api/searchservice/create-index). 

URL-адрес дополняется именем индекса `hotels`.

Для этого выполните в Postman следующие действия:

1. Измените команду на **PUT**.

2. Скопируйте этот URL-адрес: `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels?api-version=2019-05-06`.

3. Укажите в тексте запроса определение индекса (см. ниже).

4. Нажмите кнопку **Отправить**.

![Текст запроса Postman][8]

### <a name="index-definition"></a>Определение индексов

Коллекция полей определяет структуру документа. Каждый документ должен содержать эти поля, и для каждого поля должен быть указан тип данных. Строковые поля используются в полнотекстовом поиске, поэтому может потребоваться привести числовые данные как строки, если требуется, чтобы содержимое было доступно для поиска.

Атрибуты поля определяют допустимое действие. Интерфейсы REST API по умолчанию позволяют выполнять множество действий. Например, по умолчанию все строки доступны для поиска, извлечения, фильтрования и применения аспектов. Обычно атрибуты приходится указывать только для того, чтобы отключить определенные возможности.

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }

При отправке запроса вы должны получить ответ HTTP 201, указывающий, что индекс создан успешно. Это действие можно проверить на портале, однако обратите внимание, что на странице портала настроены интервалы обновления, поэтому изменения отразятся через минуту или две.

> [!TIP]
> Если вы получите код HTTP 504, проверьте, что в URL указан протокол HTTPS. Если возникают сообщения об ответных кодах 400 или 404 протокола HTTP, следует удостовериться в отсутствии ошибок при копировании. Код HTTP 403, как правило, означает, что возникла ошибка с ключом API (либо он недействительный, либо имеется ошибка синтаксиса при указании ключа).

## <a name="2---load-documents"></a>2\. Загрузка документов

Создание и заполнение индекса выполняется по отдельности. В службе "Поиск Azure" индекс содержит все данные с возможностью поиска, которые можно указать в виде документов JSON. Для этой задачи используется [REST API для добавления, обновления и удаления документов](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). 

URL-адрес дополняется коллекциями `docs` и операцией `index`.

Для этого выполните в Postman следующие действия:

1. Изменить команду на **POST**.

2. Скопируйте этот URL-адрес: `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/docs/index?api-version=2019-05-06`.

3. Укажите в тексте запроса документы JSON (см. ниже).

4. Нажмите кнопку **Отправить**.

![Полезные данные запроса Postman][10]

### <a name="json-documents-to-load-into-the-index"></a>Документы JSON для загрузки в индекс

Тело запроса содержит четыре документа, которые нужно добавить в индекс отелей.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "description_fr": "Meilleur hôtel en ville",
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
             "description_fr": "Hôtel le moins cher en ville",
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
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

Через несколько секунд в списке сеансов появится ответ HTTP 200. Это означает, что документы были успешно созданы. 

Код 207 говорит об ошибке загрузки хотя бы одного из документов. Если вы получаете код 404, текст или заголовок запроса содержат синтаксические ошибки. Убедитесь, что вы изменили конечную точку, чтобы включить `/docs/index`.

> [!Tip]
> Для выбранных источников данных можно выбрать альтернативный подход *индексатора*, упрощающий работу и уменьшающий объем кода, необходимого для индексирования. Дополнительные сведения см. в статье [Indexer operations (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) (Операции индексатора (REST API службы "Поиск Azure")).


## <a name="3---search-an-index"></a>3\. Поиск в индексе

Теперь, когда индекс и документы загружены, вы можете создавать запросы к ним, используя [REST API для поиска документов](https://docs.microsoft.com/rest/api/searchservice/search-documents).

URL-адрес дополняется строкой запроса в формате оператора поиска.

Для этого выполните в Postman следующие действия:

1. Измените команду на **GET**.

2. Скопируйте этот URL-адрес: `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2019-05-06`.

3. Нажмите кнопку **Отправить**.

Этот запрос выполняет поиск слова motel и возвращает число документов в результатах поиска. После того как вы щелкнете **Send** (Отправить), запрос и ответ для Postman будут выглядеть как на следующем снимке экрана. Код состояния должен быть 200.

 ![Ответ на запрос Postman][11]

## <a name="get-index-properties"></a>Запрос свойств индекса
Вы также можете запрашивать системную информацию для получения количества документов и занятого объема хранилища: `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/stats?api-version=2019-05-06`.

В Postman запрос должен выглядеть следующим образом, а ответ включает число документов и используемое пространство в байтах.

 ![Системный запрос Postman][12]

Обратите внимание, что синтаксис api-version отличается. Для этого запроса используйте `?` для добавления api-version. `?` отделяет URL-адрес от строки запроса, а символ & отделяет каждую пару имя=значение в строке запроса. Для этого запроса api-version — первый и единственный элемент в строке запроса.

Дополнительные сведения об этом API см. в статье о [REST API для получения статистики индекса](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics).

## <a name="clean-up"></a>Очистка

Если вы работаете в своей подписке, по окончании проекта рекомендуем решить, нужны ли вам созданные ресурсы. Работающие ресурсы могут означать лишние затраты. Можно удалить отдельные ресурсы или удалить группу ресурсов, что позволит удалить весь набор ресурсов.

Просматривать ресурсы и управлять ими можно на портале с помощью ссылок **Все ресурсы** или **Группы ресурсов** на панели навигации слева.

При работе с бесплатной версией службы помните о том, что вам доступно максимум три индекса, индексатора и источника данных. Вы можете удалить отдельные элементы на портале, чтобы не превысить лимит. 

## <a name="next-steps"></a>Дополнительная информация

Клиенты REST являются очень полезными для незапланированного исследования, но теперь, когда вы знаете, как работают REST API, можно приступить к написанию кода. Следующие шаги см. в статьях по следующим ссылкам:

+ [Краткое руководство Создание индекса службы поиска Azure с помощью пакета SDK для .NET](search-create-index-dotnet.md)
+ [Краткое руководство Создание индекса (REST) с помощью PowerShell](search-create-index-rest-api.md)

<!--Image References-->
[1]: ./media/search-get-started-postman/fiddler-url.png
[2]: ./media/search-get-started-postman/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-get-started-postman/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-get-started-postman/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-get-started-postman/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-get-started-postman/postman-url.png
[7]: ./media/search-get-started-postman/fiddler-request.png
[8]: ./media/search-get-started-postman/postman-request.png
[9]: ./media/search-get-started-postman/fiddler-docs.png
[10]: ./media/search-get-started-postman/postman-docs.png
[11]: ./media/search-get-started-postman/postman-query.png
[12]: ./media/search-get-started-postman/postman-system-query.png
