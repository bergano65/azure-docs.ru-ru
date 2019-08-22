---
title: Индексирование источника данных Azure Cosmos DB — Поиск Azure
description: Сканирование источника данных Azure Cosmos DB и прием данных в полнотекстовый индекс с поддержкой поиска в службе "Поиск Azure". Индексаторы автоматизируют прием данных из выбранных источников, таких как Azure Cosmos DB.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 802a4e9c6191d33051eb075543691845595bc9c3
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656696"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>Индексирование Cosmos DB с помощью индексатора службы поиска Azure


> [!Note]
> Поддержка API MongoDB доступна в предварительной версии и не предназначена для использования в рабочей среде. Эта функция предоставляется в [версии REST API 2019-05-06-Preview](search-api-preview.md). В настоящее время отсутствует поддержка портала или пакета SDK для .NET.
>
> API SQL общедоступен.

В этой статье показано, как настроить [индексатор](search-indexer-overview.md) Azure Cosmos DB для извлечения содержимого и сделать его поиском в службе поиска Azure. Этот рабочий процесс создает индекс службы поиска Azure и загружает его с использованием существующего текста, извлеченного из Azure Cosmos DB. 

Так как терминология может быть запутанной, стоит отметить, что [Azure Cosmos DB индексирование](https://docs.microsoft.com/azure/cosmos-db/index-overview) и [индексирование поиска Azure](search-what-is-an-index.md) являются уникальными операциями, которые уникальны для каждой службы. Прежде чем начать индексирование поиска Azure, база данных Azure Cosmos DB должна уже существовать и содержать данные.

Для индексирования содержимого Cosmos можно использовать [портал](#cosmos-indexer-portal), API-интерфейсы или пакет SDK для .NET. Индексатор Cosmos DB в службе "Поиск Azure" может выполнять обход [элементов Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) , доступных через следующие протоколы:

* [API SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference); 
* [API MongoDB (Предварительная версия)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)

> [!Note]
> Пользовательские голоса имеют существующие элементы для поддержки дополнительных API. Вы можете выполнить голосование за API-интерфейсы Cosmos, которые вы хотели бы увидеть в службе поиска Azure: [API таблиц](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab), [API Graph](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4), [API Apache Cassandra](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Использование портала

Самый простой способ индексирования элементов Azure Cosmos — использовать мастер в [портал Azure](https://portal.azure.com/). Выполнив выборку данных и читая метаданные в контейнере, мастер [**импорта данных**](search-import-data-portal.md) в службе поиска Azure может создать индекс по умолчанию, сопоставлять исходные поля с полями целевого индекса и загружать индекс в одной операции. В зависимости от размера и сложности исходных данных можно создать рабочий индекс полнотекстового поиска за считаные минуты.

Мы рекомендуем использовать одну и ту же подписку Azure как для поиска Azure, так и для Azure Cosmos DB, лучше в том же регионе.

### <a name="1---prepare-source-data"></a>1\. Подготовка исходных данных

У вас должна быть учетная запись Cosmos, база данных Cosmos Azure, сопоставленная с API SQL или API MongoDB, а также контейнер документов JSON. 

Убедитесь, что база данных Cosmos DB содержит данные. [Мастер импорта данных](search-import-data-portal.md) считывает метаданные и выполняет выборку данных для определения схемы индекса, но также загружает данные из Cosmos DB. Если данные отсутствуют, работа мастера завершается с ошибкой "ошибка обнаружения схемы индекса из источника данных: Не удалось построить индекс прототипа, так как источник данных "емптиколлектион" не вернул данные ".

### <a name="2---start-import-data-wizard"></a>2\. Запуск мастера импорта данных

[Запустить мастер](search-import-data-portal.md) можно на панели команд на странице службы поиска Azure или щелкнув **Добавить поиск Azure** в разделе **Параметры** в левой области навигации вашей учетной записи хранения.

   ![Команда импорта данных на портале](./media/search-import-data-portal/import-data-cmd2.png "Запуск мастера импорта данных")

### <a name="3---set-the-data-source"></a>3\. Настройка источника данных

> [!NOTE] 
> В настоящее время нельзя создавать или изменять источники данных **MongoDB** с помощью портал Azure или пакета SDK для .NET. Но вы **можете** отслеживать на портале историю выполнения индексаторов MongoDB.

На странице **источник данных** должен быть **Cosmos DB**источник со следующими характеристиками:

+ **Name** — это имя объекта источника данных. После создания вы можете выбрать его для других рабочих нагрузок.

+ **Cosmos DB учетная запись** должна быть основной или вторичной строкой подключения от Cosmos DB `AccountEndpoint` с помощью `AccountKey`и. Учетная запись определяет, будут ли данные приведены как API SQL или API Mongo DB.

+ **База данных** — это существующая база данных из учетной записи. 

+ **Коллекция** — это контейнер документов. Для успешности импорта должны существовать документы. 

+ Если необходимо использовать все документы, **запрос** может быть пустым, в противном случае можно ввести запрос, выбирающий подмножество документа. 

   ![Определение источника данных Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Определение источника данных Cosmos DB")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4\. Пропуск страницы "Добавление когнитивного поиска" в мастере

Для импорта документов не требуется добавлять профессиональные навыки. Если у вас нет необходимости [включать API-интерфейсы Cognitive Services и преобразования](cognitive-search-concept-intro.md) в конвейер индексирования, пропустите этот шаг.

Чтобы пропустить этот шаг, сначала перейдите на следующую страницу.

   ![Кнопка следующей страницы для когнитивного поиска](media/search-get-started-portal/next-button-add-cog-search.png)

На этой странице можно сразу перейти к настройке индекса.

   ![Пропуск шага когнитивных навыков](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5\. Настройка атрибутов индекса

На странице **Индекс** вы увидите список полей с типом данных и ряд флажков для настройки атрибутов индекса. Мастер может создать список полей на основе метаданных и выборки исходных данных. 

Можно выполнить групповое выделение атрибутов, установив флажок в верхней части столбца атрибута. Выберите возможность **извлечения** и **поиска** для каждого поля, которое должно быть возвращено клиентскому приложению и которое подлежит обработке полнотекстового поиска. Вы заметите, что целые числа не являются полным текстом или нечеткими для поиска (числа оцениваются буквально и часто используются в фильтрах).

Дополнительные сведения см. в описании [атрибутов индекса](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) и [языковых анализаторов](https://docs.microsoft.com/rest/api/searchservice/language-support) . 

Просмотрите выбранные параметры. После запуска мастера создаются структуры физических данных и вы не сможете изменить эти поля без удаления и повторного создания всех объектов.

   ![Определение индекса Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Определение индекса Cosmos DB")

### <a name="6---create-indexer"></a>6\. Создание индексатора

Полностью настроенный мастер создает три разных объекта в службе поиска. Объект источника данных и объект индекса сохраняются как именованные ресурсы в службе поиска Azure. На последнем шаге создает объект индексатора. Если присвоить индексатору имя, он будет существовать как отдельный ресурс, который можно запланировать и контролировать независимо от индекса и объекта источника данных, созданных в том же процессе мастера.

Если вы не знакомы с индексаторами, *индексатор* — это ресурс в службе поиска Azure, сканирующий внешний источник данных для поиска содержимого. Выходные данные мастера **импорта данных** — это индексатор, который выполняет обход источника данных Cosmos DB, извлекает содержимое, доступное для поиска, и импортирует его в индекс в службе поиска Azure.

На следующем снимке экрана показана конфигурация индексатора по умолчанию. Можно переключиться на один **раз** , если вы хотите запустить индексатор один раз. Нажмите кнопку **Отправить** , чтобы запустить мастер и создать все объекты. Индексирование начинается немедленно.

   ![Cosmos DB определение индексатора](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB определение индексатора")

Вы можете отслеживать импорт данных на страницах портала. Уведомления о ходе выполнения указывают состояние индексирования и количество передаваемых документов. 

По завершении индексирования можно использовать [Проводник поиска](search-explorer.md), чтобы отправить запрос индексу.

> [!NOTE]
> Если вы не видите нужные данные, может потребоваться задать дополнительные атрибуты для дополнительных полей. Удалите индекс и индексатор, которые вы только что создали, и снова пошаговые инструкции мастера, изменив параметры индексов на шаге 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Использование REST API

Вы можете использовать REST API для индексации Azure Cosmos DB данных, следуя рабочему процессу из трех частей, общим для всех индексаторов в службе поиска Azure: создание источника данных, создание индекса и создание индексатора. Извлечение данных из хранилища Cosmos происходит при отправке запроса на создание индексатора. После завершения этого запроса у вас будет индекс с запросом. 

При оценке MongoDB необходимо использовать оставшуюся `api-version=2019-05-06-Preview` для создания источника данных.

В учетной записи Cosmos DB вы можете указать, должна ли коллекция автоматически индексировать все документы. По умолчанию все документы автоматически индексируются, но вы можете отключить эту функцию. При выключенном индексировании документы могут быть доступны только через собственные ссылки или запросы, использующие идентификатор документа. Для службы "Поиск Azure" требуется включить автоматическое индексирование Cosmos DB в коллекции, которую эта служба будет индексировать. 

> [!WARNING]
> Azure Cosmos DB — это база данных DocumentDB нового поколения. Ранее с API версии **2017-11-11** можно было использовать `documentdb` синтаксис. Это означало, что можно указать тип источника данных в `cosmosdb` виде `documentdb`или. Начиная с API версии **2019-05-06** , API-интерфейсы и портал службы поиска Azure поддерживают `cosmosdb` только синтаксис, описанный в этой статье. Это означает, что тип источника данных должен `cosmosdb` иметь значение, если вы хотите подключиться к конечной точке Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1\. формирование входных данных для запроса

Для каждого запроса необходимо указать имя службы и ключ администратора для поиска Azure (в заголовке POST), а также имя и ключ учетной записи хранения для хранилища BLOB-объектов. Вы можете использовать [POST](search-get-started-postman.md) для отправки HTTP-запросов в службу поиска Azure.

Скопируйте следующие четыре значения в блокнот, чтобы их можно было вставить в запрос:

+ Имя службы поиска Azure
+ Ключ администратора поиска Azure
+ Строка подключения Cosmos DB

Эти значения можно найти на портале:

1. На страницах портала для поиска Azure скопируйте URL-адрес службы поиска на странице Обзор.

2. В левой области навигации щелкните **ключи** , а затем скопируйте либо первичный, либо вторичный ключ (они эквивалентны).

3. Перейдите на страницы портала для учетной записи хранения Cosmos. В области навигации слева в разделе **Параметры**щелкните **ключи**. На этой странице представлен URI, два набора строк подключения и два набора ключей. Скопируйте одну из строк подключения в Блокнот.

### <a name="2---create-a-data-source"></a>2\. Создание источника данных

**Источник данных** указывает данные для индексирования, учетные данные и политики для обнаружения изменений в данных (например, измененные или удаленные документы в коллекции). Источник данных определяется как независимый ресурс, который может использоваться несколькими индексаторами.

Чтобы создать источник данных, создайте запрос POST:

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
| **name** | Обязательный элемент. Выберите любое имя, представляющее объект источника данных. |
|**type**| Обязательный элемент. Этот параметр должен содержать значение `cosmosdb`. |
|**credentials** | Обязательный элемент. Должна быть Cosmos DB строкой подключения.<br/>Для коллекций SQL строки подключения имеют следующий формат:`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>Для MongoDB Collections добавьте **типа API = MongoDB** в строку подключения:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>Не рекомендуется указывать номера портов в URL-адресе конечной точки. Если вы укажете номер порта, служба поиска Azure не сможет индексировать базу данных Azure Cosmos DB.|
| **container** | Содержит следующие элементы: <br/>**name**. Обязательный элемент. Укажите идентификатор коллекции баз данных для индексирования.<br/>**query**. Необязательный элемент. Можно указать запрос на сведение произвольного документа JSON в неструктурированную схему, индексируемую поиском Azure.<br/>Для коллекций MongoDB запросы не поддерживаются. |
| **датачанжедетектионполици** | (рекомендуется). Ознакомьтесь с разделом [Индексация измененных документов](#DataChangeDetectionPolicy).|
|**датаделетиондетектионполици** | Необязательный элемент. Ознакомьтесь с разделом [удаленных документов](#DataDeletionDetectionPolicy).|

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


### <a name="3---create-a-target-search-index"></a>3\. Создание целевого индекса поиска 

[Создайте целевой индекс поиска Azure](/rest/api/searchservice/create-index) , если он еще не создан. В следующем примере создается индекс с полем ID и Description:

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
| Строковое |Edm.String |
| Массивы типов-примитивов, например [a, b, c] |Collection(Edm.String) |
| Строки, которые выглядят как даты |Edm.DateTimeOffset, Edm.String |
| Объекты GeoJSON, например { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Другие объекты JSON |Н/Д |

### <a name="4---configure-and-run-the-indexer"></a>4\. Настройка и запуск индексатора

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

Дополнительные сведения об определении расписаний индексаторов см. [в статье Планирование индексаторов для поиска Azure](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Использование .NET

Общедоступный пакет SDK для .NET имеет полное соответствие с общедоступной REST API. Мы рекомендуем прочитать предыдущий раздел о REST API, чтобы ознакомиться с концепциями, рабочим процессом и требованиями. Используйте следующую справочную документацию по .NET API для реализации индексатора JSON в управляемом коде.

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
