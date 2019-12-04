---
title: Поиск по Azure Cosmos DBным данным
titleSuffix: Azure Cognitive Search
description: Выполняет сканирование источника данных Azure Cosmos DB и прием данных в полнотекстовом индексе с возможностью поиска в Когнитивный поиск Azure. Индексаторы автоматизируют прием данных из выбранных источников, таких как Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9301da884e26a65b198c885000159c383655b2d5
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74771468"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Индексирование Cosmos DB данных с помощью индексатора в Azure Когнитивный поиск 

> [!IMPORTANT] 
> API SQL общедоступен.
> API MongoDB, API Gremlin и поддержка API Cassandra в настоящее время доступны в общедоступной предварительной версии. Для предварительной версии функции соглашение об уровне обслуживания не предусмотрено. Мы не рекомендуем использовать ее в рабочей среде. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Вы можете запросить доступ к предварительным просмотрам, заполнив [эту форму](https://aka.ms/azure-cognitive-search/indexer-preview). [Версия REST API 2019-05-06-Preview](search-api-preview.md) предоставляет предварительные версии функций. В настоящее время предоставляется ограниченная поддержка портала, но поддержка пакета SDK для .NET пока что отсутствует.

В этой статье показано, как настроить [индексатор](search-indexer-overview.md) Azure Cosmos DB для извлечения содержимого и сделать его поиском в Azure когнитивный Поиск. Этот рабочий процесс создает индекс Azure Когнитивный поиск и загружает его с помощью существующего текста, извлеченного из Azure Cosmos DB. 

Так как терминология может быть запутанной, стоит отметить, что [Azure Cosmos DB индексирование](https://docs.microsoft.com/azure/cosmos-db/index-overview) и [индексирование Azure когнитивный Поиск](search-what-is-an-index.md) являются различными операциями, уникальными для каждой службы. Перед началом индексирования Azure Когнитивный поиск необходимо, чтобы база данных Azure Cosmos DB уже существовала и содержала данные.

Индексатор Cosmos DB в Azure Когнитивный поиск может выполнять обход [Azure Cosmos DB элементов](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) , доступ к которым осуществляется через различные протоколы.

+ Для [API SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference), который является общедоступным, можно использовать [портал](#cosmos-indexer-portal), [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)или [пакет SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet).

+ Для [API MongoDB (Предварительная версия)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction) и [API Gremlin (Предварительная версия)](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)можно использовать либо [портал](#cosmos-indexer-portal) , либо [REST API версии 2019-05-06-Preview](search-api-preview.md) в вызове [CREATE индексатора (RESTful)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) для создания индексатора.

+ Для [API Cassandra (Предварительная версия)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction)можно использовать только [REST API версии 2019-05-06-Preview](search-api-preview.md) в вызове [CREATE индексатора (RESTful)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) .


> [!Note]
> Вы можете выполнить голосование на пользовательский голос для [API таблиц](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) , если вы хотите увидеть, что он поддерживается в Azure когнитивный Поиск.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Использование портала

> [!Note]
> Сейчас портал поддерживает API SQL и API MongoDB (Предварительная версия).

Самый простой способ индексирования Azure Cosmos DB элементов — использование мастера в [портал Azure](https://portal.azure.com/). Выполнив выборку данных и читая метаданные в контейнере, мастер [**импорта данных**](search-import-data-portal.md) в Azure когнитивный Поиск может создать индекс по умолчанию, сопоставлять исходные поля с целевыми полями индекса и загружать индекс в одной операции. В зависимости от размера и сложности исходных данных можно создать рабочий индекс полнотекстового поиска за считаные минуты.

Мы рекомендуем использовать один и тот же регион или расположение для Когнитивный поиск Azure и Azure Cosmos DB для снижения задержки и избежания расходов на пропускную способность.

### <a name="1---prepare-source-data"></a>1\. Подготовка исходных данных

У вас должна быть учетная запись Cosmos DB, Azure Cosmos DBная база данных, сопоставленная с API SQL, API MongoDB (Предварительная версия) или API Gremlin (Предварительная версия) и содержимое в базе данных.

Убедитесь, что база данных Cosmos DB содержит данные. [Мастер импорта данных](search-import-data-portal.md) считывает метаданные и выполняет выборку данных для определения схемы индекса, но также загружает данные из Cosmos DB. Если данные отсутствуют, работа мастера завершается с ошибкой "ошибка при обнаружении схемы индекса из источника данных: не удалось построить индекс прототипа, так как DataSource ' емптиколлектион ' не вернул данные".

### <a name="2---start-import-data-wizard"></a>2\. Запуск мастера импорта данных

Мастер можно [запустить](search-import-data-portal.md) на панели команд на странице службы когнитивный Поиск Azure или при подключении к Cosmos DB API SQL. Вы можете щелкнуть **Добавить когнитивный Поиск Azure** в разделе **Параметры** в левой области навигации учетной записи Cosmos DB.

   ![Команда "Импорт данных" на портале](./media/search-import-data-portal/import-data-cmd2.png "Запуск мастера импорта данных")

### <a name="3---set-the-data-source"></a>3\. Настройка источника данных

На странице **источник данных** должен быть **Cosmos DB**источник со следующими характеристиками:

+ **Name** — это имя объекта источника данных. После создания вы можете выбрать его для других рабочих нагрузок.

+ **Cosmos DB учетная запись** должна быть основной или вторичной строкой подключения от Cosmos DB с `AccountEndpoint` и `AccountKey`. Для MongoDB Collections добавьте **типа API = MongoDB** в конец строки подключения и отделите его от строки подключения точкой с запятой. Для API Gremlin и API Cassandra используйте инструкции для [REST API](#cosmosdb-indexer-rest).

+ **База данных** — это существующая база данных из учетной записи. 

+ **Коллекция** — это контейнер документов. Для успешности импорта должны существовать документы. 

+ Если необходимо использовать все документы, **запрос** может быть пустым, в противном случае можно ввести запрос, выбирающий подмножество документа. **Запрос** доступен только для API SQL.

   ![Определение источника данных Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Определение источника данных Cosmos DB")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4\. Пропустите страницу "обогащение содержимого" в мастере

Добавление неученых навыков (или обогащения) не является требованием импорта. Если нет необходимости [добавлять обогащение искусственного интеллекта](cognitive-search-concept-intro.md) в конвейер индексирования, этот шаг следует пропустить.

Чтобы пропустить этот шаг, щелкните синюю кнопку в нижней части страницы для "Далее" и "пропустить".

### <a name="5---set-index-attributes"></a>5\. Настройка атрибутов индекса

На странице **Индекс** вы увидите список полей с типом данных и ряд флажков для настройки атрибутов индекса. Мастер может создать список полей на основе метаданных и выборки исходных данных. 

Можно выполнить групповое выделение атрибутов, установив флажок в верхней части столбца атрибута. Выберите возможность **извлечения** и **поиска** для каждого поля, которое должно быть возвращено клиентскому приложению и которое подлежит обработке полнотекстового поиска. Вы заметите, что целые числа не являются полным текстом или нечеткими для поиска (числа оцениваются буквально и часто используются в фильтрах).

Дополнительные сведения см. в описании [атрибутов индекса](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) и [языковых анализаторов](https://docs.microsoft.com/rest/api/searchservice/language-support) . 

Просмотрите выбранные параметры. После запуска мастера создаются структуры физических данных и вы не сможете изменить эти поля без удаления и повторного создания всех объектов.

   ![Определение индекса Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Определение индекса Cosmos DB")

### <a name="6---create-indexer"></a>6\. Создание индексатора

Полностью настроенный мастер создает три разных объекта в службе поиска. Объект источника данных и объект индекса сохраняются как именованные ресурсы в службе Когнитивный поиск Azure. На последнем шаге создает объект индексатора. Если присвоить индексатору имя, он будет существовать как отдельный ресурс, который можно запланировать и контролировать независимо от индекса и объекта источника данных, созданных в том же процессе мастера.

Если вы не знакомы с индексаторами, *индексатором* является ресурс в когнитивный Поиск Azure, который обходит внешний источник данных для поиска содержимого. Выходные данные мастера **импорта данных** — это индексатор, который выполняет обход источника данных Cosmos DB, извлекает содержимое, доступное для поиска, и импортирует его в индекс на когнитивный Поиск Azure.

На следующем снимке экрана показана конфигурация индексатора по умолчанию. Можно переключиться на один **раз** , если вы хотите запустить индексатор один раз. Нажмите кнопку **Отправить** , чтобы запустить мастер и создать все объекты. Индексирование начинается немедленно.

   ![Cosmos DB определение индексатора](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB определение индексатора")

Вы можете отслеживать импорт данных на страницах портала. Уведомления о ходе выполнения указывают состояние индексирования и количество передаваемых документов. 

По завершении индексирования можно использовать [Проводник поиска](search-explorer.md), чтобы отправить запрос индексу.

> [!NOTE]
> Если вы не видите нужные данные, может потребоваться задать дополнительные атрибуты для дополнительных полей. Удалите индекс и индексатор, которые вы только что создали, и снова пошаговые инструкции мастера, изменив параметры индексов на шаге 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Использование REST API

Вы можете использовать REST API для индексации Azure Cosmos DB данных, следуя рабочему процессу из трех частей, общему для всех индексаторов в Azure Когнитивный поиск: создание источника данных, создание индекса и создание индексатора. Извлечение данных из Cosmos DB происходит при отправке запроса на создание индексатора. После завершения этого запроса у вас будет индекс с запросом. 

> [!NOTE]
> Для индексирования данных Cosmos DB API Gremlin или Cosmos DB API Cassandra сначала необходимо запросить доступ к предварительным вым, заполнив [эту форму](https://aka.ms/azure-cognitive-search/indexer-preview). После обработки запроса вы получите инструкции по использованию [REST API версии 2019-05-06-Preview](search-api-preview.md) для создания источника данных.

Ранее в этой статье упоминалось, что [Azure Cosmos DB индексирование](https://docs.microsoft.com/azure/cosmos-db/index-overview) и индексирование когнитивный Поиск индексированием [Azure](search-what-is-an-index.md) являются разными операциями. Для Cosmos DB индексирования по умолчанию все документы автоматически индексируются, за исключением API Cassandra. При отключении автоматического индексирования доступ к документам можно получить только через свои собственные ссылки или запросы с помощью идентификатора документа. Для индексирования Когнитивный поиск Azure Cosmos DB автоматическое индексирование должно быть включено в коллекции, которая будет индексироваться Когнитивный поиск Azure. При регистрации в предварительной версии индексатора Cosmos DB API Cassandra вам будут предоставлены инструкции по настройке индексирования Cosmos DB.

> [!WARNING]
> Azure Cosmos DB — это база данных DocumentDB нового поколения. Ранее с API версии **2017-11-11** можно было использовать синтаксис `documentdb`. Это означало, что можно указать тип источника данных `cosmosdb` или `documentdb`. Начиная с API версии **2019-05-06** , API-интерфейсы и портал Azure когнитивный Поиск поддерживают только синтаксис `cosmosdb`, как описано в этой статье. Это означает, что тип источника данных должен `cosmosdb`, если вы хотите подключиться к конечной точке Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1\. формирование входных данных для запроса

Для каждого запроса необходимо указать имя службы и ключ администратора для Azure Когнитивный поиск (в заголовке POST), а также имя и ключ учетной записи хранения для хранилища BLOB-объектов. Вы можете использовать [POST](search-get-started-postman.md) для отправки HTTP-запросов в Azure когнитивный Поиск.

Скопируйте следующие четыре значения в блокнот, чтобы их можно было вставить в запрос:

+ Имя службы Когнитивный поиск Azure
+ Ключ администратора Когнитивный поиск Azure
+ Строка подключения Cosmos DB

Эти значения можно найти на портале:

1. На страницах портала Когнитивный поиск Azure скопируйте URL-адрес службы поиска на странице Обзор.

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
| **name** | Обязательный параметр. Выберите любое имя, представляющее объект источника данных. |
|**type**| Обязательный параметр. Этот параметр должен содержать значение `cosmosdb`. |
|**credentials** | Обязательный параметр. Должна быть Cosmos DB строкой подключения.<br/>Для коллекций SQL строки подключения имеют следующий формат: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>Для MongoDB Collections добавьте **типа API = MongoDB** в строку подключения:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>Чтобы получить доступ к предварительной версии и сведения о том, как отформатировать учетные данные, для графов Gremlin и таблиц Cassandra Зарегистрируйте [предварительную версию для создания условного индексатора](https://aka.ms/azure-cognitive-search/indexer-preview) .<br/><br/>Не рекомендуется указывать номера портов в URL-адресе конечной точки. Если указать номер порта, Когнитивный поиск Azure не сможет индексировать базу данных Azure Cosmos DB.|
| **container** | Содержит следующие элементы: <br/>**name**: обязательное поле. Укажите идентификатор коллекции баз данных для индексирования.<br/>**query**: необязательное поле. Можно указать запрос на сведение произвольного документа JSON в плоскую схему, которую Azure Когнитивный поиск может индексировать.<br/>Для API MongoDB, API Gremlin и API Cassandra запросы не поддерживаются. |
| **датачанжедетектионполици** | (рекомендуется). Ознакомьтесь с разделом [Индексация измененных документов](#DataChangeDetectionPolicy).|
|**датаделетиондетектионполици** | Необязательный элемент. Ознакомьтесь с разделом [удаленных документов](#DataDeletionDetectionPolicy).|

### <a name="using-queries-to-shape-indexed-data"></a>Использование запросов для формирования индексированных данных
Вы можете указать SQL-запрос для преобразования вложенных свойств или массивов в плоскую структуру, проецирования свойств JSON, а также для фильтрации данных, подлежащих индексированию. 

> [!WARNING]
> Пользовательские запросы не поддерживаются для **API MongoDB**, **api Gremlin**и **API Cassandra**: параметру `container.query` должно быть присвоено значение null или опущено. Если вам нужно использовать пользовательский запрос, сообщите нам об этом через сайт [User Voice](https://feedback.azure.com/forums/263029-azure-search).

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

[Создайте целевой индекс Azure когнитивный Поиск](/rest/api/searchservice/create-index) , если он еще не создан. В следующем примере создается индекс с полем ID и Description:

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
> Для секционированных коллекций ключ документа по умолчанию — это свойство `_rid` Azure Cosmos DB, которое Azure Когнитивный поиск автоматически переименовывается в `rid`, поскольку имена полей не могут начинаться с символа подчеркивания. Кроме того, Azure Cosmos DB `_rid` значения содержат символы, недопустимые в ключах Azure Когнитивный поиск. Поэтому значения `_rid` представлены в кодировке Base64.
> 
> Для коллекций MongoDB Azure Когнитивный поиск автоматически переименовывает свойство `_id` в `id`.  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Сопоставление типов данных JSON и типов данных Когнитивный поиск Azure
| Тип данных JSON | Совместимые типы полей целевого индекса |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Числа, которые выглядят как целые числа |Edm.Int32, Edm.Int64, Edm.String |
| Числа, которые выглядят как числа с плавающей запятой |Edm.Double, Edm.String |
| Строка |Edm.String |
| Массивы типов-примитивов, например [a, b, c] |Collection(Edm.String) |
| Строки, которые выглядят как даты |Edm.DateTimeOffset, Edm.String |
| Геообъекты JSON, например { "тип": "Точка", "координаты": [ долгота, широта ] } |Edm.GeographyPoint |
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

Дополнительные сведения об определении расписания индексатора см. [в разделе Планирование индексаторов для когнитивный Поиск Azure](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Использование .NET

Общедоступный пакет SDK для .NET имеет полное соответствие с общедоступной REST API. Мы рекомендуем прочитать предыдущий раздел о REST API, чтобы ознакомиться с концепциями, рабочим процессом и требованиями. Используйте следующую справочную документацию по .NET API для реализации индексатора JSON в управляемом коде.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Индексация измененных документов

Политика обнаружения изменения данных предназначена для эффективного определения измененных элементов данных. В настоящее время единственной поддерживаемой политикой является [`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) , использующая свойство `_ts` (timestamp), предоставляемое Azure Cosmos DB, которое указывается следующим образом:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Для обеспечения хорошей производительности индексатора мы настоятельно рекомендуем использовать эту политику. 

При использовании пользовательского запроса, убедитесь, что свойство `_ts` проецируется в запросе.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Инкрементное индексирование и пользовательские запросы

Инкрементное индексирование позволяет восстановить работу индексатора при следующем запуске с того места, в котором она была прервана из-за временных сбоев или ограничения времени выполнения, и избежать таким образом повторного индексирования всей коллекции с нуля. Это особенно важно для индексации больших коллекций. 

Чтобы обеспечить инкрементное индексирование при использовании пользовательского запроса, убедитесь, что запрос упорядочивает результаты по столбцу `_ts`. Это позволяет периодически проверять, что Когнитивный поиск Azure использует для обеспечения инкрементного хода выполнения в случае сбоев.   

В некоторых случаях, даже если запрос содержит предложение `ORDER BY [collection alias]._ts`, Azure Когнитивный поиск может не определить, что запрос упорядочен по `_ts`. Вы можете сообщить Azure Когнитивный поиск, что результаты упорядочены с помощью свойства конфигурации `assumeOrderByHighWaterMarkColumn`. Чтобы сделать это, создайте или обновите индексатор, как показано в этом примере: 

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

Поздравляем! Вы узнали, как интегрировать Azure Cosmos DB с Azure Когнитивный поиск с помощью индексатора.

* Дополнительные сведения об Azure Cosmos DB см. на [странице этой службы](https://azure.microsoft.com/services/cosmos-db/).
* Дополнительные сведения об Azure Когнитивный поиск см. на [странице службы поиска](https://azure.microsoft.com/services/search/).
