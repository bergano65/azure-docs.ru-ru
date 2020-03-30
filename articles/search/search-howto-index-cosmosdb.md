---
title: Поиск данных Azure Cosmos DB
titleSuffix: Azure Cognitive Search
description: Импортируйте данные из DB Azure Cosmos в поисковый индекс в Azure Cognitive Search. Индексаторы автоматизируют прием данных из выбранных источников, таких как Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: d1723b6c5d56554fbff576f6a07e37455845bda4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283008"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>How to index Cosmos DB data using an indexer in Azure Cognitive Search (Индексирование данных Cosmos DB с помощью индексатора в службе "Когнитивный поиск Azure") 

> [!IMPORTANT] 
> API СЗЛ, как правило, доступны.
> В настоящее время в открытом доступе находятся API MongoDB, API Gremlin и поддержка API Cassandra. Для предварительной версии функции соглашение об уровне обслуживания не предусмотрено. Мы не рекомендуем использовать ее в рабочей среде. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Вы можете запросить доступ к превью, заполнив [эту форму.](https://aka.ms/azure-cognitive-search/indexer-preview) [Версия REST API 2019-05-06-Preview](search-api-preview.md) предоставляет предварительные версии функций. Сейчас доступна ограниченная поддержка портала. Поддержка пакета SDK для .NET пока не реализована.

> [!WARNING]
> Только коллекции Cosmos DB с [политикой индексирования,](https://docs.microsoft.com/azure/cosmos-db/index-policy) установленной для [последовательности,](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) поддерживаются Azure Cognitive Search. Индексирование коллекций с политикой ленивого индексирования не рекомендуется и может привести к отсутствующим данным. Коллекции с отключением индексации не поддерживаются.

В этой статье показано, как настроить [индексатор](search-indexer-overview.md) Azure Cosmos DB для извлечения содержимого и сделать его пригодным для поиска в Azure Cognitive Search. Этот рабочий процесс создает индекс когнитивного поиска Azure и загружает его существующим текстом, извлеченным из Azure Cosmos DB. 

Поскольку терминология может ввести в заблуждение, стоит отметить, что [индексация DB Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/index-overview) и [индексирование когнитивного поиска Azure](search-what-is-an-index.md) являются отдельными операциями, уникальными для каждой службы. Перед началом индексации Azure Cognitive Search база данных Azure Cosmos DB должна уже существовать и содержать данные.

Индексатор Cosmos DB в Azure Cognitive Search может просматривать [элементы Azure Cosmos DB,](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) доступ к ним осуществляется с помощью различных протоколов. 

+ Для [API,](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference)который, как правило, доступен, вы можете использовать [портал,](#cosmos-indexer-portal) [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)или [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet) для создания источника данных и индекса.

+ Для [MongoDB API (предварительный просмотр)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)вы можете использовать либо [портал,](#cosmos-indexer-portal) либо [версию REST API 2019-05-06-Preview](search-api-preview.md) для создания источника данных и индекса.

+ Для [Cassandra API (предварительный просмотр)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction) и [API Gremlin (предварительный просмотр)](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)можно использовать [версию REST API 2019-05-06-Preview](search-api-preview.md) только для создания источника данных и индекса.


> [!Note]
> Вы можете проголосовать за пользовательский голос за [API таблицы,](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) если хотите, чтобы он был поддержан в Azure Cognitive Search.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Использование портала

> [!Note]
> В настоящее время портал поддерживает API и MongoDB API (предварительный просмотр).

Самый простой способ индексирования элементов Azure Cosmos DB — использовать мастера на [портале Azure.](https://portal.azure.com/) При выборке данных и чтении метаданных в контейнере мастер [**данных импорта**](search-import-data-portal.md) в Azure Cognitive Search может создать индекс по умолчанию, сопоставить поля исходного кода для целевых полей индекса и загрузить индекс в одну операцию. В зависимости от размера и сложности исходных данных можно создать рабочий индекс полнотекстового поиска за считаные минуты.

Мы рекомендуем использовать один и тот же регион или местоположение для Azure Cognitive Search и Azure Cosmos DB для снижения задержки и для избежания платы за пропускную способность.

### <a name="1---prepare-source-data"></a>1. Подготовка исходных данных

Учетная запись Cosmos DB должна быть отображена базой данных Azure Cosmos DB на API СИ-Л, API MongoDB (предварительный просмотр) или API Gremlin (предварительный просмотр) и содержимое базы данных.

Убедитесь, что ваша база данных Cosmos DB содержит данные. [Мастер данных импорта](search-import-data-portal.md) считывает метаданные и выполняет выборку данных, чтобы сделать вывод о схеме индекса, но он также загружает данные из Cosmos DB. Если данные отсутствуют, мастер останавливается с этой ошибкой "Ошибка обнаружения схемы индекса из источника данных: Не удалось построить прототип индекса, потому что источник данных 'пустой сбор' вернулся нет данных".

### <a name="2---start-import-data-wizard"></a>2. Запуск мастера импорта данных

Вы можете [запустить мастера](search-import-data-portal.md) из панели команд на странице службы когнитивного поиска Azure, или если вы подключитесь к Cosmos DB S'L API, вы можете нажать **Добавить Azure Cognitive Search** в разделе **Настройки** левой навигационной панели учетной записи Cosmos DB.

   ![Команда данных импорта на портале](./media/search-import-data-portal/import-data-cmd2.png "Запуск мастера импорта данных")

### <a name="3---set-the-data-source"></a>3. Настройка источника данных

На странице **источника данных** источником должен быть **Cosmos DB**со следующими спецификациями:

+ **Имя** — это имя объекта источника данных. После создания его можно выбрать для других рабочих нагрузок.

+ **Cosmos DB счет** должен быть основной или вторичной строки соединения из Космос DB, с `AccountEndpoint` и `AccountKey`. Для коллекций MongoDB добавьте **ApiKind-MongoDb** в конец строки соединения и отделите ее от строки соединения с запятой. Для API Gremlin и Cassandra API используйте инструкции для [REST API.](#cosmosdb-indexer-rest)

+ **База данных** — это существующая база данных из учетной записи. 

+ **Коллекция** представляет собой контейнер с документами. Документы должны существовать для того, чтобы импорт увенчался успехом. 

+ **Запрос** может быть пустым, если требуется все документы, в противном случае вы можете ввести запрос, который выбирает подмножество документов. **Запрос** доступен только для API S'L.

   ![Определение источника исходных данных Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Определение источника исходных данных Cosmos DB")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - Пропустить страницу "Обогащение содержимого" в мастере

Добавление когнитивных навыков (или обогащения) не является требованием импорта. Если у вас нет конкретной необходимости [добавлять обогащение ИИ](cognitive-search-concept-intro.md) в конвейер индексирования, вы должны пропустить этот шаг.

Чтобы пропустить шаг, нажмите синие кнопки в нижней части страницы для "Следующий" и "Пропустить".

### <a name="5---set-index-attributes"></a>5. Настройка атрибутов индекса

На странице **Индекс** вы увидите список полей с типом данных и ряд флажков для настройки атрибутов индекса. Мастер может создать список полей на основе метаданных и выборки исходных данных. 

Вы можете навалом выбрать атрибуты, нажав на флажок в верхней части столбца атрибута. Выберите **Retrievable** и **Searchable** для каждого поля, которое должно быть возвращено в клиентское приложение и при условии полной обработки текста. Вы заметите, что целые числа не полный текст или нечеткой поиска (цифры оцениваются дословно и часто полезны в фильтрах).

Для получения дополнительной информации просмотрите описание [атрибутов индекса](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) и [языковых анализаторов.](https://docs.microsoft.com/rest/api/searchservice/language-support) 

Просмотрите выбранные параметры. После запуска мастера создаются структуры физических данных и вы не сможете изменить эти поля без удаления и повторного создания всех объектов.

   ![Определение индекса Космос dB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Определение индекса Космос dB")

### <a name="6---create-indexer"></a>6. Создание индексатора

Полностью настроенный мастер создает три разных объекта в службе поиска. Объект источника данных и объект индекса сохраняются как именованные ресурсы в службе когнитивного поиска Azure. На последнем шаге создает объект индексатора. Если присвоить индексатору имя, он будет существовать как отдельный ресурс, который можно запланировать и контролировать независимо от индекса и объекта источника данных, созданных в том же процессе мастера.

Если вы не знакомы с индексаторами, *индексатор* — это ресурс в Azure Cognitive Search, который сканирует внешний источник данных для поиска содержимого. Выход мастера **данных импорта** — это индексер, который сканирует ваш источник данных Cosmos DB, извлекает содержимое поиска и импортирует его в индекс в Azure Cognitive Search.

На следующем скриншоте показана конфигурация индекса по умолчанию. Вы можете переключиться на **Один раз,** если вы хотите запустить индексатор один раз. Нажмите **Отправить,** чтобы запустить мастера и создать все объекты. Индексирование начинается немедленно.

   ![Определение индекса индекса Космоса DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Определение индекса индекса Космоса DB")

Вы можете отслеживать импорт данных на страницах портала. Уведомления о ходе выполнения указывают состояние индексирования и количество передаваемых документов. 

По завершении индексирования можно использовать [Проводник поиска](search-explorer.md), чтобы отправить запрос индексу.

> [!NOTE]
> Если вы не видите ожидаемые данные, возможно, потребуется установить больше атрибутов на большем количестве полей. Удалите только что созданный индекс и индексер и повторите через мастер, изменив выбор атрибутов индекса в шаге 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Использование REST API

Можно использовать REST API для индексирования данных Azure Cosmos DB после трех частей рабочего процесса, общего для всех индексаторов в Azure Cognitive Search: создать источник данных, создать индекс, создать индекс. Извлечение данных из Cosmos DB происходит при отправке запроса Create Indexer. После завершения этого запроса у вас будет запрашиваемый индекс. 

> [!NOTE]
> Для индексирования данных от Cosmos DB Gremlin API или Cosmos DB Cassandra API необходимо сначала запросить доступ к закрытому превью, заполнив [эту форму.](https://aka.ms/azure-cognitive-search/indexer-preview) Как только ваш запрос будет обработан, вы получите инструкции о том, как использовать [версию REST API 2019-05-06-Preview](search-api-preview.md) для создания источника данных.

Ранее в этой статье упоминалось, что [индексация DB Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/index-overview) и [индексация индексирования когнитивного поиска Azure Cognitive](search-what-is-an-index.md) Search являются отдельными операциями. Для индексации Cosmos DB по умолчанию все документы автоматически индексируются, за исключением API Cassandra. При отключении автоматической индексации к документам можно получить доступ только по их автоссылке или по запросам с помощью идентификатора документа. Индексация Когнитивного поиска Azure требует автоматической индексации Cosmos DB в коллекции, которая будет проиндексирована Azure Cognitive Search. При регистрации на предварительный просмотр индекса API Cosmos DB Cassandra API вам будут даны инструкции о том, как настроить индексацию Cosmos DB.

> [!WARNING]
> Azure Cosmos DB — это база данных DocumentDB нового поколения. Ранее с версией API **2017-11-11** можно было использовать `documentdb` синтаксис. Это означало, что вы можете `cosmosdb` `documentdb`указать тип источника данных как или . Начиная с версии API **2019-05-06,** а API Azure Cognitive Search и Portal поддерживают только `cosmosdb` синтаксис в соответствии с инструкциями в этой статье. Это означает, что тип `cosmosdb` источника данных должен быть подключен к конечной точке Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1 - Сборка входных данных для запроса

Для каждого запроса необходимо учесть имя службы и ключ администратора для Azure Cognitive Search (в заголовке POST), а также имя учетной записи и ключ для хранения капли. Вы можете использовать [Postman](search-get-started-postman.md) для отправки запросов HTTP в Azure Cognitive Search.

Скопируйте следующие четыре значения в блокнот, чтобы вы могли вставить их в запрос:

+ Имя службы когнитивного поиска Azure
+ Ключ админ-поиска Azure Cognitive
+ Строка соединения Космос DB

Вы можете найти эти значения на портале:

1. На страницах портала для Azure Cognitive Search скопируйте URL поискового сервиса со страницы «Обзор».

2. В левом панели навигации щелкните **ключи,** а затем скопируйте либо основной или вторичный ключ (они эквивалентны).

3. Переключитесь на страницы портала для учетной записи хранилища Cosmos. В левом навигационном стеле, под **настройками,** щелкните **клавиши.** Эта страница содержит URI, два набора строк соединения и два набора ключей. Копируйте одну из строк соединения с блокнотом.

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
| **name** | Обязательный элемент. Выберите любое имя для представления объекта источника данных. |
|**тип**| Обязательный элемент. Этот параметр должен содержать значение `cosmosdb`. |
|**Учетные данные** | Обязательный элемент. Должно быть, строка соединения Космос DB.<br/>Для коллекций S'L строки соединения находятся в этом формате:`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/> Для коллекций MongoDB добавьте **ApiKind=MongoDb** в строку подключения: <br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>Для графиков Gremlin и таблиц Cassandra подпишитесь на [предварительный просмотр закрытого указателя,](https://aka.ms/azure-cognitive-search/indexer-preview) чтобы получить доступ к предварительному просмотру и информации о том, как форматировать учетные данные.<br/><br/>Не рекомендуется указывать номера портов в URL-адресе конечной точки. Если вы укажете номер порта, Azure Cognitive Search не сможет индексировать базу данных Azure Cosmos DB.|
| **Контейнер** | В данной вкладке содержатся следующие элементы. <br/>**имя**: Требуется. Укажите идентификатор коллекции баз данных для индексации.<br/>**query**: необязательное поле. Можно указать запрос, чтобы сгладить произвольный документ JSON в плоскую схему, которую может проиндексировать Azure Cognitive Search.<br/>Для API MongoDB, Gremlin API и Cassandra API запросы не поддерживаются. |
| **dataChangeDetectionPolicy** | (рекомендуется). Ознакомьтесь с разделом [Индексация измененных документов](#DataChangeDetectionPolicy).|
|**данныеDeletionDetectionPolicy** | Необязательный параметр. Ознакомьтесь с разделом [удаленных документов](#DataDeletionDetectionPolicy).|

### <a name="using-queries-to-shape-indexed-data"></a>Использование запросов для формирования индексированных данных
Вы можете указать SQL-запрос для преобразования вложенных свойств или массивов в плоскую структуру, проецирования свойств JSON, а также для фильтрации данных, подлежащих индексированию. 

> [!WARNING]
> Пользовательские запросы не поддерживаются для **MongoDB API,** **Gremlin API**и **Cassandra API:** `container.query` параметр должен быть установлен на нуле или опущен. Если вам нужно использовать пользовательский запрос, сообщите нам об этом через сайт [User Voice](https://feedback.azure.com/forums/263029-azure-search).

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

[Создайте целевой индекс когнитивного поиска Azure,](/rest/api/searchservice/create-index) если его у вас еще нет. Следующий пример создает индекс с полем идентификатора и описания:

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
> Для разделенных коллекций ключом документа по умолчанию является `_rid` свойство Azure Cosmos DB, которое Azure Cognitive Search автоматически переименовывает, поскольку имена полей не могут начинаться `rid` с подчеркивания символа. Кроме того, значения Azure Cosmos DB `_rid` содержат символы, недействительные в клавишах Azure Cognitive Search. Поэтому значения `_rid` представлены в кодировке Base64.
> 
> Для коллекций MongoDB Azure Cognitive `_id` Search `id`автоматически переименовывает свойство в .  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Картирование между типами данных JSON и типами когнитивного поиска Azure
| Тип данных JSON | Совместимые типы полей целевого индекса |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Числа, которые выглядят как целые числа |Edm.Int32, Edm.Int64, Edm.String |
| Числа, которые выглядят как числа с плавающей запятой |Edm.Double, Edm.String |
| Строка |Edm.String |
| Массивы типов-примитивов, например [a, b, c] |Collection(Edm.String) |
| Строки, которые выглядят как даты |Edm.DateTimeOffset, Edm.String |
| Геообъекты JSON, например { "тип": "Точка", "координаты": [ долгота, широта ] } |Edm.GeographyPoint |
| Другие объекты JSON |Недоступно |

### <a name="4---configure-and-run-the-indexer"></a>4 - Настройка и запуск индекса

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

Для получения дополнительной информации об [How to schedule indexers for Azure Cognitive Search](search-howto-schedule-indexers.md)определении расписания индексаторов см.

## <a name="use-net"></a>Использование .NET

Общедоступный .NET SDK имеет полный паритет с общедоступным REST API. Мы рекомендуем прочитать предыдущий раздел о REST API, чтобы ознакомиться с концепциями, рабочим процессом и требованиями. Используйте следующую справочную документацию по .NET API для реализации индексатора JSON в управляемом коде.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Индексация измененных документов

Политика обнаружения изменения данных предназначена для эффективного определения измененных элементов данных. В настоящее время единственной поддерживаемой политикой является [`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) использование `_ts` (метки времени) свойства, предоставленного Azure Cosmos DB, которое указывается следующим образом:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Для обеспечения хорошей производительности индексатора мы настоятельно рекомендуем использовать эту политику. 

При использовании пользовательского запроса, убедитесь, что свойство `_ts` проецируется в запросе.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Инкрементное индексирование и пользовательские запросы

Инкрементное индексирование позволяет восстановить работу индексатора при следующем запуске с того места, в котором она была прервана из-за временных сбоев или ограничения времени выполнения, и избежать таким образом повторного индексирования всей коллекции с нуля. Это особенно важно для индексации больших коллекций. 

Чтобы обеспечить инкрементное индексирование при использовании пользовательского запроса, убедитесь, что запрос упорядочивает результаты по столбцу `_ts`. Это позволяет периодически проверять, что Azure Cognitive Search использует для обеспечения постепенного прогресса при наличии сбоев.   

В некоторых случаях, даже если `ORDER BY [collection alias]._ts` запрос содержит пункт, Azure Cognitive Search может не `_ts`сделать вывод о том, что запрос заказан запросом Вы можете сказать Azure Cognitive Search, `assumeOrderByHighWaterMarkColumn` что результаты упорядочены с помощью свойства конфигурации. Чтобы сделать это, создайте или обновите индексатор, как показано в этом примере: 

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

## <a name="next-steps"></a><a name="NextSteps"></a>Дальнейшие действия

Поздравляем! Вы узнали, как интегрировать Azure Cosmos DB с Azure Cognitive Search с помощью индекса.

* Дополнительные сведения об Azure Cosmos DB см. на [странице этой службы](https://azure.microsoft.com/services/cosmos-db/).
* Чтобы узнать больше о когнитивном поиске Azure, смотрите [страницу службы поиска](https://azure.microsoft.com/services/search/).
