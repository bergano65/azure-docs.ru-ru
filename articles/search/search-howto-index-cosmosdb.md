---
title: Индексирование источника данных Azure Cosmos DB — Поиск Azure
description: Сканирование источника данных Azure Cosmos DB и прием данных в полнотекстовый индекс с поддержкой поиска в службе "Поиск Azure". Индексаторы автоматизируют прием данных из выбранных источников, таких как Azure Cosmos DB.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 7f9df42725e41fb514370dbdb828ad5b1305ea78
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485456"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>Как индексировать Cosmos DB с помощью индексатора поиска Azure


> [!Note]
> Поддержка MongoDB API находится в предварительной версии и не предназначена для использования в рабочей среде. Эта функция предоставляется в [версии REST API 2019-05-06-Preview](search-api-preview.md). Нет, портал или поддержки пакета SDK для .NET в настоящее время.
>
> SQL API является общедоступным.

В этой статье показано, как настроить Azure Cosmos DB [индексатора](search-indexer-overview.md) на извлечение содержимого и сделать его доступным для поиска в службе поиска Azure. Этот рабочий процесс создает индекс поиска Azure и загружает в него существующий текст, извлекаемый из Azure Cosmos DB. 

Поскольку терминология может показаться довольно запутанным, стоит отметить, что [индексирование Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) и [индексирование поиска Azure](search-what-is-an-index.md) отличаются, уникальные для каждой службы. Перед началом поиска Azure индексирования, базе данных Azure Cosmos DB должен уже существовать и содержат данные.

Можно использовать [портала](#cosmos-indexer-portal), API REST или пакета SDK для .NET для индексирования Cosmos содержимого. Индексатор поиска Azure Cosmos DB может просканировать [Azure Cosmos элементы](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) через эти протоколы:

* [API SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference); 
* [API MongoDB (Предварительная версия)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)

> [!Note]
> User Voice имеет существующие элементы, для получения дополнительной поддержки API. Можно привести голос для API-интерфейсов Cosmos, вы бы хотели см. в разделе поддерживаемых в службе поиска Azure: [API таблицы](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab), [Graph API](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4), [Apache Cassandra API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Использование портала

Самым простым способом для индексирования Azure Cosmos элементов — использовать мастер в [портала Azure](https://portal.azure.com/). Данные выборки и чтения метаданных для контейнера, [ **импорта данных** ](search-import-data-portal.md) мастера в службе поиска Azure можно создать индекс по умолчанию, сопоставление полей источника для полей целевого индекса и загрузить индекс в одном операция. В зависимости от размера и сложности исходных данных можно создать рабочий индекс полнотекстового поиска за считаные минуты.

Мы рекомендуем использовать той же подписке Azure для службы поиска Azure и Azure Cosmos DB, желательно в том же регионе.

### <a name="1---prepare-source-data"></a>1\. Подготовка исходных данных

Вы должны иметь учетную запись Cosmos, к базе данных Azure Cosmos, сопоставленных с SQL API или MongoDB API и контейнером документов JSON. 

Убедитесь, что база данных Cosmos DB содержит данные. [Мастера импорта данных](search-import-data-portal.md) считывает метаданные и выполняет выборки данных, определить схему индекса, но также загружает данные из Cosmos DB. Если отсутствуют данные, мастер останавливается с этой ошибкой «ошибка определение схемы индекса в источнике данных: Не удалось создать прототипа индекса, так как источник данных «emptycollection» не вернул данных».

### <a name="2---start-import-data-wizard"></a>2\. Запуск мастера импорта данных

Вы можете [запустить мастер](search-import-data-portal.md) на панели команд, на странице службы поиска Azure, или щелкнув **добавить поиск Azure** в **параметры** левой части вашей учетной записи хранения Панель навигации.

   ![Команда импорта данных на портале](./media/search-import-data-portal/import-data-cmd2.png "Запуск мастера импорта данных")

### <a name="3---set-the-data-source"></a>3\. Настройка источника данных

> [!NOTE] 
> В настоящее время не удается создать или изменить **MongoDB** источников данных с помощью портала Azure или пакет SDK для .NET. Но вы **можете** отслеживать на портале историю выполнения индексаторов MongoDB.

В **источника данных** страницы, источник должен быть **Cosmos DB**, со следующими спецификациями:

+ **Имя** имя объекта источника данных. После создания вы можете его для других рабочих нагрузок.

+ **Учетная запись Cosmos DB** должен быть строкой подключения первичного или вторичного из Cosmos DB с `AccountEndpoint` и `AccountKey`. Учетная запись определяет ли данные приводятся как SQL API или API MongoDB

+ **База данных** является существующей базы данных из учетной записи. 

+ **Коллекция** является контейнером документов. Документы должен существовать в порядке для для успешного импорта. 

+ **Запрос** можно оставить пустым, если нужно, чтобы все документы, в противном случае можно ввести запрос, который выбирает подмножество документа. 

   ![Определение источника данных Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "определение источника данных Cosmos DB")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4\. Пропуск страницы "Добавление когнитивного поиска" в мастере

Добавление когнитивные навыки не требуется для импорта документа. Если у вас нет необходимости [включать API-интерфейсы Cognitive Services и преобразования](cognitive-search-concept-intro.md) в конвейер индексирования, пропустите этот шаг.

Чтобы пропустить шаг, сначала перейдите к следующей странице.

   ![Кнопка следующей страницы для когнитивного поиска](media/search-get-started-portal/next-button-add-cog-search.png)

С этой страницы можно перейти к настройке индекса.

   ![Пропуск шага когнитивных навыков](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5\. Настройка атрибутов индекса

На странице **Индекс** вы увидите список полей с типом данных и ряд флажков для настройки атрибутов индекса. Мастер создаст список полей, на основе метаданных и при выборке данных источника. 

Вы может массового выберите атрибуты, установив флажок над столбцом атрибутов. Выберите **доступный для получения** и **доступный для поиска** для каждого поля, который должен быть возвращен для клиентского приложения и подлежит обработки полнотекстового поиска. Вы заметите, что целых чисел не полный текст или Нечеткий поиск для поиска (числа вычисляются открытым текстом и которые часто используются в фильтрах).

Просмотрите описание [атрибуты индекса](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) и [языковых анализаторов](https://docs.microsoft.com/rest/api/searchservice/language-support) Дополнительные сведения. 

Просмотрите выбранные параметры. После запуска мастера создаются структуры физических данных и вы не сможете изменить эти поля без удаления и повторного создания всех объектов.

   ![Определение индекса Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "определение индекса Cosmos DB")

### <a name="6---create-indexer"></a>6\. Создание индексатора

Полностью настроенный мастер создает три разных объекта в службе поиска. Объект источника данных и объект индекса сохраняются как именованные ресурсы в службе поиска Azure. На последнем шаге создает объект индексатора. Если присвоить индексатору имя, он будет существовать как отдельный ресурс, который можно запланировать и контролировать независимо от индекса и объекта источника данных, созданных в том же процессе мастера.

Если вы не знакомы с индексаторами, *индексатор* — это ресурс в службе поиска Azure, сканирующий внешний источник данных для поиска содержимого. Выходные данные **импорта данных** мастер является индексатором, который выполняет поиск источника данных Cosmos DB, извлекает содержимое для поиска и импортирует его в индекс поиска Azure.

На следующем снимке экрана показаны конфигурации индексатора по умолчанию. Можно переключиться на **один раз** Если вы хотите запустить индексатор один раз. Нажмите кнопку **отправить** для запуска мастера и создания всех объектов. Индексирование начинается немедленно.

   ![Определение индексатора для Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "определение индексатора для Cosmos DB")

Вы можете отслеживать импорт данных на страницах портала. Уведомления о ходе выполнения указывают состояние индексирования и количество передаваемых документов. 

По завершении индексирования можно использовать [Проводник поиска](search-explorer.md), чтобы отправить запрос индексу.

> [!NOTE]
> Если вы не видите нужные данные, может потребоваться установить дополнительные атрибуты для дополнительных полей. Удаление индекса и индексатора, вы только что создали и следуйте инструкциям в мастере, изменив выбранные параметры атрибуты индекса на шаге 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Использование REST API

Можно использовать REST API для индексации данных Azure Cosmos DB, следуя трехкомпонентного рабочему процессу общие для всех индексаторов в службе поиска Azure: Создание источника данных, создание индекса, создайте индексатор. Извлечение данных из хранилища Cosmos происходит, когда при отправке запроса Создание индексатора. После завершения этого запроса будет иметь поддерживает запросы индекс. 

При оценке MongoDB, необходимо использовать REST `api-version=2019-05-06-Preview` для создания источника данных.

В учетной записи Cosmos DB вы можете указать, должна ли коллекция автоматически индексировать все документы. По умолчанию все документы автоматически индексируются, но вы можете отключить эту функцию. При выключенном индексировании документы могут быть доступны только через собственные ссылки или запросы, использующие идентификатор документа. Для службы "Поиск Azure" требуется включить автоматическое индексирование Cosmos DB в коллекции, которую эта служба будет индексировать. 

> [!WARNING]
> Azure Cosmos DB — это база данных DocumentDB нового поколения. Ранее с помощью версии API **2017-11-11** можно использовать `documentdb` синтаксис. Это означало, что можно указать типа источника данных, как `cosmosdb` или `documentdb`. Начиная с версии API **2019-05-06** портал и API-интерфейсов поиска Azure поддерживают только `cosmosdb` синтаксис, как описано в этой статье. Это означает, что тип источника данных должны `cosmosdb` Если вы хотите подключаться к конечной точке Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1 — Создание входных данных для запроса

Для каждого запроса необходимо указать имя службы и ключ администратора для службы поиска Azure (в заголовке POST) и имя учетной записи хранения и ключ для хранилища BLOB-объектов. Можно использовать [Postman](search-get-started-postman.md) для отправки HTTP-запросы в службу поиска Azure.

Скопируйте следующие четыре значения в Блокнот, чтобы их можно вставить в запрос:

+ Имя службы поиска Azure
+ Ключ администратора Azure поиска
+ Строка подключения Cosmos DB

Эти значения можно найти на портале:

1. На страницах портала для службы поиска Azure скопируйте URL-адрес службы поиска на странице обзора.

2. В области навигации слева щелкните **ключи** и скопируйте либо первичный или вторичный ключ (они эквивалентны).

3. Переключитесь на страницы портала для вашей учетной записи Cosmos. В области навигации слева в разделе **параметры**, нажмите кнопку **ключи**. Эта страница содержит URI, два набора строк подключения, и два набора ключей. Скопируйте один из строки подключения в Блокнот.

### <a name="2---create-a-data-source"></a>2 - Создание источника данных

**Источник данных** указывает данные для индексирования, учетные данные и политики для обнаружения изменений в данных (например, измененные или удаленные документы в коллекции). Источник данных определяется как независимый ресурс, который может использоваться несколькими индексаторами.

Чтобы создать источник данных, сформулируйте запрос POST:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

Текст запроса содержит определение источника данных, который должен включать следующие поля.

| Поле   | Описание |
|---------|-------------|
| **name** | Обязательный элемент. Выберите любое имя для представления объектом источника данных. |
|**type**| Обязательный элемент. Этот параметр должен содержать значение `cosmosdb`. |
|**credentials** | Обязательный элемент. Должен быть строкой подключения Cosmos DB.<br/>Для коллекций SQL строки подключения, в следующем формате: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>Для коллекций MongoDB добавьте **типа API = MongoDb** строку подключения:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>Не рекомендуется указывать номера портов в URL-адресе конечной точки. Если вы укажете номер порта, служба поиска Azure не сможет индексировать базу данных Azure Cosmos DB.|
| **container** | содержит следующие элементы: <br/>**name**. Обязательный элемент. Укажите идентификатор коллекции базы данных для индексирования.<br/>**query**. Необязательный элемент. Можно указать запрос на сведение произвольного документа JSON в неструктурированную схему, индексируемую поиском Azure.<br/>Для коллекций MongoDB запросы не поддерживаются. |
| **dataChangeDetectionPolicy** | (рекомендуется). Ознакомьтесь с разделом [Индексация измененных документов](#DataChangeDetectionPolicy).|
|**dataDeletionDetectionPolicy** | Необязательный элемент. Ознакомьтесь с разделом [удаленных документов](#DataDeletionDetectionPolicy).|

### <a name="using-queries-to-shape-indexed-data"></a>Использование запросов для формирования индексированных данных
Вы можете указать SQL-запрос для преобразования вложенных свойств или массивов в плоскую структуру, проецирования свойств JSON, а также для фильтрации данных, подлежащих индексированию. 

> [!WARNING]
> Пользовательские запросы не поддерживаются для коллекций **MongoDB**. Задайте для параметра `container.query` значение NULL или пропустите его. Если вам нужно использовать пользовательский запрос, сообщите нам об этом через сайт [User Voice](https://feedback.azure.com/forums/263029-azure-search).

Пример документа:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

Запрос на фильтрацию:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Преобразование запросов:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Запрос на проектирование:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Массив преобразованных запросов:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 - Создание целевого индекса поиска 

[Создайте целевой индекс поиска Azure](/rest/api/searchservice/create-index) Если вы не сделали. В следующем примере создается индекс с Идентификатором и описанием полем:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Убедитесь, что схема целевого индекса совместима с исходными документами JSON или выходными данными настраиваемой проекции запроса.

> [!NOTE]
> Для секционированных коллекций в качестве ключа документа по умолчанию используется свойство `_rid` Azure Cosmos DB. Служба "Поиск Azure" автоматически переименовывает его в `rid`, так как имена полей не могут начинаться с символа подчеркивания. Кроме того, значения `_rid` в Azure Cosmos DB содержат символы, недопустимые в ключах службы "Поиск Azure". Поэтому значения `_rid` представлены в кодировке Base64.
> 
> Для коллекций MongoDB служба "Поиск Azure" автоматически переименовывает свойство `_id` в `doc_id`.  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Сопоставление типов данных JSON и типов данных службы поиска Azure
| Тип данных JSON | Совместимые типы полей целевого индекса |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Числа, которые выглядят как целые числа |Edm.Int32, Edm.Int64, Edm.String |
| Числа, которые выглядят как числа с плавающей запятой |Edm.Double, Edm.String |
| String |Edm.String |
| Массивы типов-примитивов, например [a, b, c] |Collection(Edm.String) |
| Строки, которые выглядят как даты |Edm.DateTimeOffset, Edm.String |
| Объекты GeoJSON, например { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Другие объекты JSON |Н/Д |

### <a name="4---configure-and-run-the-indexer"></a>4 - Настройка и выполнение индексатора

Когда индекс и источник данных уже созданы, можно создать индексатор:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Этот индексатор выполняется каждые два часа (интервал расписания имеет значение PT2H). Чтобы запускать индексатор каждые 30 минут, задайте интервал "PT30M". Самый короткий интервал, который можно задать, составляет 5 минут. Расписание является необязательным. Если оно не указано, то индексатор выполняется только один раз при его создании. Однако индексатор можно запустить по запросу в любое время.   

Дополнительные сведения об API создания индексатора см. в разделе [Создание индексатора](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Дополнительные сведения об определении расписания индексаторов см. в разделе [планирование индексаторы поиска Azure](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Использование .NET

Общедоступными пакетами SDK для .NET имеет полного соответствия с общедоступными API REST. Мы рекомендуем прочитать предыдущий раздел о REST API, чтобы ознакомиться с концепциями, рабочим процессом и требованиями. Используйте следующую справочную документацию по .NET API для реализации индексатора JSON в управляемом коде.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Индексация измененных документов

Политика обнаружения изменения данных предназначена для эффективного определения измененных элементов данных. Сейчас поддерживается только политика `High Water Mark`, использующая свойство метки времени `_ts`, предоставленное Azure Cosmos DB. Эта политика указывается следующим образом.

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Для обеспечения хорошей производительности индексатора мы настоятельно рекомендуем использовать эту политику. 

При использовании пользовательского запроса, убедитесь, что свойство `_ts` проецируется в запросе.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Инкрементное индексирование и пользовательские запросы

Инкрементное индексирование позволяет восстановить работу индексатора при следующем запуске с того места, в котором она была прервана из-за временных сбоев или ограничения времени выполнения, и избежать таким образом повторного индексирования всей коллекции с нуля. Это особенно важно для индексации больших коллекций. 

Чтобы обеспечить инкрементное индексирование при использовании пользовательского запроса, убедитесь, что запрос упорядочивает результаты по столбцу `_ts`. Это позволяет выполнять периодическое сохранение контрольных точек, которые Поиск Azure использует для обеспечения инкрементного индексирования при наличии ошибок.   

В некоторых случаях, даже если запрос содержит предложение `ORDER BY [collection alias]._ts`, Поиск Azure может не определить, что запрос упорядочен по столбцу `_ts`. С помощью свойства конфигурации `assumeOrderByHighWaterMarkColumn` можно сообщить Поиску Azure, что результаты сортируются. Чтобы сделать это, создайте или обновите индексатор, как показано в этом примере: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Индексация удаленных документов

Строки, удаляемые из исходной коллекции, вероятно, также следует удалить из индекса поиска. Политика обнаружения удаления данных предназначена для эффективного определения удаленных элементов данных. В настоящее время единственной поддерживаемой политикой является политика `Soft Delete` (удаление помечается особым флагом), которая указывается следующим образом:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

При использовании пользовательского запроса, убедитесь, что свойство на которое указывает `softDeleteColumnName`, проецируется в запросе.

В следующем примере создается источник данных с политикой мягкого удаления:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>Дальнейшие действия

Поздравляем! Вы узнали, как интегрировать Azure Cosmos DB со службой "Поиск Azure" с помощью индексатора.

* Дополнительные сведения об Azure Cosmos DB см. на [странице этой службы](https://azure.microsoft.com/services/cosmos-db/).
* Дополнительные сведения о службе поиска Azure см. на [этой странице](https://azure.microsoft.com/services/search/).
