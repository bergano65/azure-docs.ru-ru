---
title: Поиск данных Azure S'L
titleSuffix: Azure Cognitive Search
description: Импортные данные из базы данных Azure S'L с использованием индексеров для полного поиска текста в Azure Cognitive Search. В этой статье описываются подключения, конфигурация индексатора и прием данных.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c09727e8d92a449b41124eae6ad8381d66cb2619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113305"
---
# <a name="connect-to-and-index-azure-sql-database-content-using-an-azure-cognitive-search-indexer"></a>Подключение к базе данных Azure S'L с помощью индекса когнитивного поиска Azure и индексировать его с помощью указателя когнитивного поиска Azure

Прежде чем вы сможете заразить [индекс когнитивного поиска Azure,](search-what-is-an-index.md)необходимо заполнить его своими данными. Если данные хранятся в базе данных Azure S'L, **индексатор когнитивного поиска Azure для базы данных Azure S'L** (или **индексер Azure S'L** для краткости) может автоматизировать процесс индексации, что означает меньше кода для записи и меньше инфраструктуры для ухода.

В этой статье рассматривается механизм использования [индексаторов](search-indexer-overview.md), а также подробно описываются функции, доступные только в базах данных SQL Azure (например, интегрированное отслеживание изменений). 

В дополнение к базам данных Azure S'L, Azure Cognitive Search предоставляет индексы для [хранения Azure Cosmos DB,](search-howto-index-cosmosdb.md) [Azure Blob](search-howto-indexing-azure-blob-storage.md)и [таблицы Azure.](search-howto-indexing-azure-tables.md) Чтобы запросить поддержку для других источников данных, предоставьте свои отзывы на [форуме обратной связи Azure Cognitive Search.](https://feedback.azure.com/forums/263029-azure-search/)

## <a name="indexers-and-data-sources"></a>Индексаторы и источники данных

**Источник данных** определяет, какие данные следует индексировать, какие учетные данные требуются для доступа к данным и какие политики нужны, чтобы эффективно выявлять изменения в данных (новые, измененные или удаленные строки). Он определяется как независимый ресурс, который могут использовать несколько индексаторов.

**Индексатор** — это ресурс, соединяющий один источник данных с целевым индексом поиска. Индексатор используется в следующих случаях.

* однократное копирование данных для заполнения индекса;
* добавление в индекс изменений в источнике данных по расписанию;
* запуск по требованию для обновления индекса при необходимости.

Один индексатор может использовать только одну таблицу или представление, однако можно создать несколько индексаторов, если вы хотите заполнить несколько индексов поиска. Дополнительные сведения см. в статье [Indexer operations (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow) (Операции с индексаторами в REST API службы поиска Azure).

Индексатор SQL Azure можно установить и настроить с помощью:

* мастера импорта данных на [портале Azure](https://portal.azure.com);
* Azure Когнитивный поиск [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Azure Когнитивный поиск [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

В этой статье мы будем использовать REST API для создания **индексаторов** и **источников данных**.

## <a name="when-to-use-azure-sql-indexer"></a>Когда следует использовать индексатор SQL Azure
Уместность использования индексатора Azure SQL зависит от нескольких факторов, связанных с данными. Если данные соответствуют следующим требованиям, вы можете использовать индексатор SQL Azure.

| Критерии | Сведения |
|----------|---------|
| Источником данных является отдельная таблица или представление | Если данные разбиты по нескольким таблицам, можно создать одно представление данных. Однако при использовании представления вы не сможете использовать интегрированное отслеживание изменений SQL Server для обновления индекса с помощью добавочных изменений. Дополнительные сведения см. в разделе [Запись измененных и удаленных строк](#CaptureChangedRows) ниже. |
| Типы данных совместимы | Большинство, но не все типы S'L поддерживаются в индексе когнитивного поиска Azure. Список см. в разделе [Сопоставление типов данных](#TypeMapping). |
| Синхронизация данных в режиме реального времени необязательна | Индексатор может выполнять повторное индексирование таблицы не чаще, чем раз в пять минут. При частом изменении данных, которые должны быть отражены в индексе в течение нескольких секунд или минут, мы рекомендуем использовать [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) или [пакет SDK для .NET](search-import-data-dotnet.md), чтобы отправлять обновленные строки напрямую. |
| Возможно добавочное индексирование | Если у вас есть большой набор данных и планируется запустить индексер по расписанию, Azure Cognitive Search должен быть в состоянии эффективно идентифицировать новые, измененные или удаленные строки. Недобавочное индексирование разрешено только при индексировании по требованию или при индексации менее 100 000 строк. Дополнительные сведения см. в разделе [Запись измененных и удаленных строк](#CaptureChangedRows) ниже. |

> [!NOTE] 
> Azure Cognitive Search поддерживает только аутентификацию сервера S'L. Если требуется поддержка для аутентификации Azure Active Directory, проголосуйте за это [предложение UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Создание индексатора SQL Azure

1. Создайте источник данных:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Вы можете получить строку подключения на [портале Azure](https://portal.azure.com). Используйте вариант `ADO.NET connection string`.

2. Создайте целевой индекс когнитивного поиска Azure, если его у вас еще нет. Создать индекс можно с помощью [портала](https://portal.azure.com) или [API создания индекса](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Убедитесь, что схема целевого индекса совместима со схемой исходной таблицы - см. [отображение между типами данных поиска S'L и Azure Cognitive.](#TypeMapping)

3. Создайте индексатор, задав ему имя и связав источник данных с целевым индексом:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

У индексатора, созданного таким образом, нет расписания. Он автоматически выполняется один раз сразу после создания. Вы можете снова выполнить его в любой момент с помощью запроса на **запуск индексатора** :

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2019-05-06
    api-key: admin-key

Вы можете настроить несколько аспектов поведения индексатора, например размер пакета и сколько документов можно пропустить, прежде чем выполнение индексатора завершится с ошибкой. Чтобы узнать больше, ознакомьтесь с [API создания индексатора](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Вам может потребоваться разрешить службам Azure подключаться к вашей базе данных. Сведения о том, как это сделать, см. в разделе [Подключение из Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

Для наблюдения за состоянием индексатора и журналом выполнения (количество проиндексированных элементов, ошибки и т. д.) используйте запрос на получение **состояния индексатора**:

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2019-05-06
    api-key: admin-key

Ответ должен выглядеть так:

    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

Журнал выполнения включает до 50 записей о недавно завершенных запусках, которые сортируются в обратном хронологическом порядке (то есть в ответе первым отображается последний запуск).
Дополнительные сведения об ответе см. [здесь](https://go.microsoft.com/fwlink/p/?LinkId=528198).

## <a name="run-indexers-on-a-schedule"></a>Запуск индексаторов по расписанию
Вы также можете организовать запуск индикатора по расписанию. Для этого добавьте свойство **schedule** при создании или обновлении индексатора. В примере ниже показан PUT- запрос для обновления индексатора:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Параметр **interval** обязателен. Он указывает время между двумя последовательными запусками индексатора. Наименьшее допустимое значение — 5 минут, наибольшее — один день. Значение должно быть отформатировано как значение dayTimeDuration XSD (ограниченное подмножество значения [продолжительности ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Используется следующий шаблон: `P(nD)(T(nH)(nM))`. Примеры: `PT15M` для каждых 15 минут, `PT2H` для каждых 2 часов.

Для получения дополнительной информации об определении расписания индекса см. [Как запланировать индексы для Azure Cognitive Search.](search-howto-schedule-indexers.md)

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Запись новых, измененных и удаленных строк

Azure Cognitive Search использует **инкрементную индексацию,** чтобы избежать необходимости переиндексировать всю таблицу или просматривать каждый раз, когда индексатор работает. Azure Cognitive Search предоставляет две политики обнаружения изменений для поддержки поэтапной индексации. 

### <a name="sql-integrated-change-tracking-policy"></a>Интегрированная политика отслеживания изменений SQL
Если ваша база данных S'L поддерживает [отслеживание изменений,](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)мы рекомендуем использовать **политику комплексного отслеживания изменений.** Это наиболее эффективная политика. Кроме того, он позволяет Azure Cognitive Search идентифицировать удаленные строки без необходимости добавлять в таблицу явную колонку "мягкое удаление".

#### <a name="requirements"></a>Требования 

+ Требования к версии базы данных:
  * SQL Server 2012 SP3 и более поздних версий, если вы используете SQL Server на виртуальных машинах Azure.
  * База данных SQL Azure 12, если вы используете базу данных SQL Azure.
+ Только таблицы (представлений нет). 
+ В базе данных [включите отслеживание изменений](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) таблицы. 
+ Нет составного первичного ключа (первичный ключ, содержащий более одного столбца) в таблице.  

#### <a name="usage"></a>Использование

Чтобы использовать эту политику, создайте или обновите источник данных следующим образом:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

При использовании интегрированной политики отслеживания изменений SQL не указывайте отдельную политику обнаружения удаления данных, так как она уже поддерживает выявление удаленных строк. Однако, чтобы автоматически обнаруживать удаления, ключ документа в индексе поиска должен быть таким же, что и основной ключ в таблице SQL. 

> [!NOTE]  
> При использовании [TRUNCATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/truncate-table-transact-sql) для удаления большого количества строк из таблицы SQL необходимо, чтобы индексатор был [сброшен](https://docs.microsoft.com/rest/api/searchservice/reset-indexer), чтобы сбросить состояние отслеживания изменений для сбора удалений строк.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Политика обнаружения изменений максимального уровня

Политика обнаружения изменений зависит от столбца изменений максимального уровня, записывающего версию или время последнего обновления строки. При использовании представления следует использовать политику максимального уровня. Столбец максимального уровня должен соответствовать следующим требованиям.

#### <a name="requirements"></a>Требования 

* при каждой вставке указывается значение для столбца;
* при всех обновлениях элементов также изменяется значение столбца;
* значение этого столбца растет с каждой вставкой или обновлением;
* Возможно эффективное выполнение запросов со следующими предложениями WHERE и ORDER BY: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`.

> [!IMPORTANT] 
> Настоятельно рекомендуется использовать тип данных [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) для столбца максимального уровня. В случае использования любого другого типа данных не гарантируется запись всех изменений при наличии транзакций, выполняемых параллельно с запросом индексатора. При использовании **rowversion** в конфигурации с репликами только для чтения необходимо указать индексатор на первичной реплике. Только первичная реплика может использоваться для сценариев синхронизации данных.

#### <a name="usage"></a>Использование

Чтобы использовать политику верхнего предела, создайте или обновите источник данных следующим образом.

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> Если в исходной таблице нет индекса на столбце высокой отметки воды, запросы, используемые индексатором S'L, могут отойти. В частности, `ORDER BY [High Water Mark Column]` положение требует эффективного выполнения индекса, когда таблица содержит много строк.
>
>

Если возникают ошибки времени ожидания, можно использовать параметр конфигурации индексатора `queryTimeout`, чтобы задать время ожидания запроса, превышающее время ожидания по умолчанию, равное 5 минутам. Например, чтобы задать время ожидания, равное 10 минутам, создайте или обновите индексатор, используя приведенную ниже конфигурацию.

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Можно также отключить предложение `ORDER BY [High Water Mark Column]`. Однако это не рекомендуется, так как в случае прерывания выполнения индексатора из-за ошибки ему придется повторно обработать все строки при последующем запуске, даже если на момент прерывания почти все из них уже были обработаны. Чтобы отключить предложение `ORDER BY`, используйте параметр `disableOrderByHighWaterMarkColumn` в определении индексатора.  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Политика обнаружения обратимого удаления столбца
Строки, удаляемые из исходной таблицы, вероятно, также следует удалить из индекса поиска. Если вы используете интегрированную политику отслеживания изменений SQL, это происходит автоматически. Однако политика отслеживания изменений максимального уровня не затрагивает удаленные строки. Что делать?

Если строки физически удалены из таблицы, Azure Cognitive Search не может сделать вывод о наличии записей, которые больше не существуют.  Тем не менее можно использовать метод обратимого удаления, чтобы логически удалять строки, не удаляя их из таблицы. Добавьте специальный столбец в таблицу или представление и помечайте удаленные строки с помощью этого столбца.

При использовании метода обратимого удаления можно указать соответствующую политику во время создания или обновления источника данных:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

Параметр **softDeleteMarkerValue** должен быть строкой. Используйте строковое представление действительного значения. Например, если имеется столбец целочисленных значений, в котором удаленные строки помечаются значением 1, то следует использовать `"1"`. Если имеется битовый столбец, в котором удаленные строки помечаются логическим значением true, то используйте строковый литерал `True` или `true`, при этом регистр значения не имеет.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>Картирование между типами данных когнитивного поиска S'L и Azure
| Тип данных SQL | Совместимые типы полей целевого индекса | Примечания |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| BIGINT |Edm.Int64, Edm.String | |
| real, float |Edm.Double, Edm.String | |
| smallmoney, money decimal numeric |Edm.String |Azure Cognitive Search не поддерживает преобразование десятичных типов в Edm.Double, поскольку это потеряет точность |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Для заполнения поля Collection(Edm.String) может использоваться строка SQL, если она представляет массив строк JSON: `["red", "white", "blue"]`. |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| geography |Edm.GeographyPoint |Поддерживаются только географические объекты типа POINT с SRID 4326 (значение по умолчанию). |
| rowversion |Недоступно |Столбцы версии строк не могут храниться в индексе поиска, но их можно использовать для отслеживания изменений. |
| time, timespan, binary, varbinary, image, xml, geometry, CLR types |Недоступно |Не поддерживается |

## <a name="configuration-settings"></a>Параметры конфигурации
Индексатор SQL предоставляет несколько параметров конфигурации.

| Параметр | Тип данных | Назначение | Значение по умолчанию |
| --- | --- | --- | --- |
| queryTimeout |строка |Задает время ожидания для выполнения запроса SQL. |5 мин ("00:05:00") |
| disableOrderByHighWaterMarkColumn |bool |Указывает SQL-запросу, используемому политикой верхнего предела, опустить предложение ORDER BY. Ознакомьтесь с [политикой верхнего предела](#HighWaterMarkPolicy). |false |

Эти параметры используются в объекте `parameters.configuration` в определении индексатора. Например, чтобы задать время ожидания запроса, равное 10 минутам, создайте или обновите индексатор, используя приведенную ниже конфигурацию.

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>часто задаваемые вопросы

**В: Могу ли я использовать индексер Azure S'L с базами данных S'L, работающими на ВМ IaaS в Azure?**

Да. Тем не менее необходимо разрешить службе поиска подключаться к базе данных. Для получения дополнительной информации [см. Нанастройку от индекса когнитивного поиска Azure к серверу S'L на Azure VM.](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

**Вопрос. Можно ли использовать индексатор SQL Azure с локальными базами данных SQL?**

Не напрямую. Мы не рекомендуем и не поддерживаем прямое соединение, так как в таком случае вам придется открыть свои базы данных для интернет-трафика. Клиенты преуспели в этом сценарии, используя технологии моста, такие как фабрика данных Azure. Для получения дополнительной информации смотрите [данные Push в индекс когнитивного поиска Azure с помощью Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector)

**В: Могу ли я использовать индексатор Azure S'L с базами данных, не работающими в IaaS на Azure?**

Нет. Мы не поддерживаем этот сценарий, так как мы не проверяли работу индексатора с базами данных не из SQL Server.  

**В: Могу ли я создать несколько индексеров, работающих по расписанию?**

Да. Но на одном узле одновременно может выполнять обработку только один индексатор. Если вам требуется, чтобы одновременно работали несколько индексаторов, вы можете расширить службу поиска, добавив несколько модулей поиска.

**В: Влияет ли работа указателя на рабочую нагрузку на запрос?**

Да. Индексатор выполняется на одном из узлов службы поиска, и ресурсы этого узла распределяются между выполнением индексирования, обслуживанием трафика запросов и другими запросами API. Если вы проводите интенсивную индексацию и обработку заданий и сталкиваетесь с высокой частотой ошибок в 503 или увеличением времени отклика, подумайте о [расширении службы поиска.](search-capacity-planning.md)

**Вопрос. Можно ли использовать вторичную реплику в [отказоустойчивом кластере](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) в качестве источника данных?**

Здесь возможны варианты в зависимости от обстоятельств. Для полной индексации таблицы или представления можно использовать вторичные реплики. 

Для поэтапной индексации Azure Cognitive Search поддерживает две политики обнаружения изменений: интегрированное отслеживание изменений и отметку high Water Mark.

В репликах только для чтения база данных SQL не поддерживает интегрированное отслеживание изменений. Таким образом необходимо использовать политику максимального уровня. 

Стандартно рекомендуется использовать тип данных rowversion для столбца максимального уровня. Однако использование rowversion зависит от функции `MIN_ACTIVE_ROWVERSION` базы данных SQL, которая не поддерживается репликами только для чтения. Таким образом необходимо направить индексатор на первичную реплику при использовании rowversion.

При попытке использовать rowversion с репликой только для чтения появится следующая ошибка: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**Вопрос. Можно ли использовать другой столбец, не rowversion, для отслеживания изменений максимального уровня?**

Не рекомендуется. Только **rowversion** обеспечивает надежную синхронизацию данных. Но в зависимости от логики приложения это может быть безопасно, если:

+ Можно гарантировать, что при запуске индекса на столе нет невыполненных транзакций, которые индексируются (например, все обновления таблицы происходят как пакет по расписанию, и расписание индекса azure Cognitive Search настроено, чтобы избежать перекрытия со таблицей график обновления).  

+ Вы периодически выполняете полное повторное индексирование, чтобы обнаружить все пропущенные строки. 