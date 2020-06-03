---
title: API SQL для Azure Cosmos DB. Примеры для пакета SDK Java версии 4
description: Поиск примеров Java на GitHub для типичных задач с помощью API SQL для Azure Cosmos DB, включая операции CRUD.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: db291b735ed6456a007446ad23373ff25e806aab
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727169"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>API SQL для Azure Cosmos DB. Примеры для пакета SDK Java версии 4

> [!div class="op_single_selector"]
> * [Примеры пакета SDK для .NET версии 2](sql-api-dotnet-samples.md)
> * [Примеры пакета SDK для .NET версии 3](sql-api-dotnet-v3sdk-samples.md)
> * [Примеры для пакета SDK Java версии 4](sql-api-java-sdk-samples.md)
> * [Примеры Node.js](sql-api-nodejs-samples.md)
> * [Примеры Python](sql-api-python-samples.md)
> * [Коллекция примеров кода Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> Дополнительные сведения о пакете SDK Java версии 4 см. в [заметках о выпуске](sql-api-sdk-java-v4.md) для пакета SDK Java для Azure Cosmos DB версии 4, [репозитории Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), [рекомендациях по повышению производительности для пакета SDK Java для Azure Cosmos DB версии 4](performance-tips-java-sdk-v4-sql.md), а также в [руководстве по устранению неполадок для пакета SDK Java версии 4 для Azure Cosmos DB версии 4](troubleshoot-java-sdk-v4-sql.md). Если в настоящее время используется более ранняя версия, чем версия 4, ознакомьтесь с руководством [Перевод приложения на использование пакета средств разработки Java для Azure Cosmos DB версии 4](migrate-java-v4-sdk.md), чтобы получить сведения об обновлении до версии 4.
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- Вы можете [активировать преимущества подписчика Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): в вашей подписке Visual Studio каждый месяц зачисляются деньги на счет, которые можно использовать для оплаты служб Azure.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

Новейшие примеры приложений, которые выполняют операции CRUD и другие распространенные операции с ресурсами Azure Cosmos DB, содержатся в репозитории GitHub [azure-cosmos-java-sql-api-samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples). Эта статья содержит:

* Ссылки на задачи в каждом из примеров файлов проектов Java. 
* Ссылки на соответствующие справочные материалы по API.

**Предварительные требования**

Чтобы запустить пример приложения, необходимы следующие компоненты:

* комплект разработчика Java 8;
* Пакет SDK Java версии 4 для Azure Cosmos DB

При необходимости можно использовать Maven, чтобы получить последние двоичные файлы пакета SDK Java версии 4 для Azure Cosmos DB для использования в этом проекте. Maven автоматически добавляет все необходимые зависимости. В противном случае можно непосредственно скачать зависимости, которые перечислены в файле pom.xml, и добавить их в путь сборки.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Запуск примеров приложений**

Клонирование примера репозитория:
```bash
$ git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples.git

$ cd azure-cosmos-java-sql-api-samples
```

Примеры можно запустить, используя интегрированною среду разработки (Eclipse, IntelliJ или VSCODE), или с помощью командной строки в Maven.

Необходимо задать эти переменные среды:

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account master key
```

Это предоставит примерам доступ на чтение и запись для вашей учетной записи.

Чтобы запустить пример, укажите его основной класс: 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

В качестве *sample.synchronicity.MainClass* может использоваться:
* crudquickstart.sync.SampleCRUDQuickstart
* crudquickstart.async.SampleCRUDQuickstartAsync
* indexmanagement.sync.SampleIndexManagement
* indexmanagement.async.SampleIndexManagementAsync
* storedprocedure.sync.SampleStoredProcedure
* storedprocedure.async.SampleStoredProcedureAsync
* changefeed.SampleChangeFeedProcesso *(Для канала изменений доступен только пример асинхронности, пример синхронности отсутствует.)* и т. д…

> [!NOTE]
> Каждый пример является самодостаточным, он устанавливается самостоятельно и выполняет необходимую очистку после удаления. В примерах выполняется несколько вызовов для создания `CosmosContainer`. Каждый раз, когда это происходит, вам выставляется счет за 1 час использования каждого уровня производительности создаваемой коллекции. 
> 
> 

## <a name="database-examples"></a>Примеры баз данных
В файле [Database CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java) показано, как выполнять следующие задачи. Чтобы узнать больше о базах данных Azure Cosmos перед выполнением приведенных ниже примеров, ознакомьтесь с тематической статьей [Работа с базами данных, контейнерами и элементами](databases-containers-items.md). 

| Задача | Справочник по API |
| --- | --- |
| [Создание базы данных](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L76-L84) | CosmosClient.createDatabaseIfNotExists |
| [Чтение базы данных по идентификатору](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L86-L94) | CosmosClient.getDatabase |
| [Чтение всех баз данных](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L96-L111) | CosmosClient.readAllDatabases |
| [Удаление базы данных](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L113-L122) | CosmosDatabase.delete |

## <a name="collection-examples"></a>Примеры коллекций
В файле [Collection CRUD Samples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) показано, как выполнять следующие задачи. Чтобы узнать больше о коллекциях Azure Cosmos перед выполнением приведенных ниже примеров, ознакомьтесь с тематической статьей [Работа с базами данных, контейнерами и элементами](databases-containers-items.md).

| Задача | Справочник по API |
| --- | --- |
| [Создание коллекции](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L94-106) | CosmosDatabase.createContainerIfNotExists |
| [Изменение настроенной производительности коллекции](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L108-116) | CosmosContainer.replaceProvisionedThroughput |
| [Получение коллекции по идентификатору](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L118-126) | CosmosDatabase.getContainer |
| [Чтение всех коллекций в базе данных](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L128-143) | CosmosDatabase.readAllContainers |
| [Удаление коллекции](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L145-154) | CosmosContainer.delete |

## <a name="autoscale-collection-examples"></a>Примеры автомасштабирования коллекций

Чтобы получить дополнительные сведений об автомасштабировании перед выполнением этих примеров, ознакомьтесь с инструкциями по включению автомасштабирования в [учетной записи](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/) и в [базах данных и контейнерах](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale).

В файле [autoscale Database CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java) показано, как выполнять следующие задачи.

| Задача | Справочник по API |
| --- | --- |
| [Создание базы данных с указанной максимальной автомасштабируемой пропускной способностью](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L76-L87) | CosmosClient.CreateDatabase<br>ThroughputProperties.createAutoscaledThroughput |

В файле [autoscale Collection CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java) показано, как выполнять следующие задачи. 

| Задача | Справочник по API |
| --- | --- |
| [Создание коллекции с указанной максимальной автомасштабируемой пропускной способностью](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L95-L107) | CosmosDatabase.createContainerIfNotExists |
| [Изменение настроенной максимальной автомасштабируемой пропускной способности коллекции](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L109-L117) | CosmosContainer.replaceThroughput |
| [Чтение конфигурации автомасштабируемой пропускной способности коллекции](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L119-L130) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>Примеры коллекции аналитического хранилища

В файле [Analytical storage Collection CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java) показано, как выполнять следующие задачи. Чтобы узнать о коллекциях Azure Cosmos перед выполнением приведенных ниже примеров, ознакомьтесь со сведениями об Azure Cosmos DB Synapse и аналитическом хранилище.

| Задача | Справочник по API |
| --- | --- |
| [Создание коллекции](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L94-L109) | CosmosDatabase.createContainerIfNotExists |

## <a name="document-examples"></a>Примеры документов
В файле [Document CRUD Samples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) показано, как выполнять следующие задачи. Чтобы узнать больше о документах Azure Cosmos перед выполнением приведенных ниже примеров, ознакомьтесь с тематической статьей [Работа с базами данных, контейнерами и элементами](databases-containers-items.md).

| Задача | Справочник по API |
| --- | --- |
| [Создание документа](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L127-L141) | CosmosContainer.createItem |
| [Чтение документа по идентификатору](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L143-L154) | CosmosContainer.readItem |
| [Чтение всех документов в коллекции](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L156-171) | CosmosContainer.readAllItems |
| [Запрос документов](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L173-L187) | CosmosContainer.queryItems |
| [Замена документа](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L189-L204) | CosmosContainer.replaceItem |
| [Выполнение операции Upsert для документа](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L206-L219) | CosmosContainer.upsertItem |
| [Удаление документа](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L302-L310) | CosmosContainer.deleteItem |
| [Замена документа с помощью условной проверки ETag](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L221-L261) | AccessCondition.setType<br>AccessCondition.setCondition |
| [Чтение документа только в том случае, если он был изменен](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L263-L300) | AccessCondition.setType<br>AccessCondition.setCondition |

## <a name="indexing-examples"></a>Примеры индексирования
В файле [Collection CRUD Samples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) показано, как выполнять следующие задачи. Чтобы узнать больше об индексировании в Azure Cosmos DB перед выполнением приведенных ниже примеров, ознакомьтесь с тематическими статьями, посвященными [политикам](index-policy.md), [типам](index-types.md) и [путям индексирования](index-paths.md). 

| Задача | Справочник по API |
| --- | --- |
| Исключение документа из индекса | ExcludedIndex<br>IndexingPolicy |
| Использование отложенного индексирования | IndexingPolicy.IndexingMode |
| [Включение указанных путей к документам в индексе](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L142-L147) | IndexingPolicy.IncludedPaths |
| [Исключение указанных путей к документам из индекса](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L149-L154) | IndexingPolicy.ExcludedPaths |
| [Создание составного индекса](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L170-L188) | IndexingPolicy.setCompositeIndexes<br>CompositePath |
| Принудительное выполнение операции проверки диапазона в хэш-индексированном пути | FeedOptions.EnableScanInQuery |
| Использование диапазонных индексов в строках | IndexingPolicy.IncludedPaths<br>RangeIndex |
| Преобразование индекса | - |
| [Создание геопространственного индекса](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L156-L168) | IndexingPolicy.setSpatialIndexes<br>SpatialSpec<br>SpatialType |

Дополнительные сведения об индексации Azure Cosmos DB см. в [этой статье](index-policy.md).

## <a name="query-examples"></a>Примеры запросов
В файле [Query Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java) показано, как выполнять следующие задачи с помощью грамматики SQL-запроса. Чтобы узнать больше об SQL-запросах в Azure Cosmos DB перед выполнением приведенных ниже примеров, ознакомьтесь со статьей о [примерах SQL-запросов для Azure Cosmos DB](how-to-sql-query.md). 

| Задача | Справочник по API |
| --- | --- |
| [Запрос всех документов](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L194-L198) | CosmosContainer.queryItems |
| [Выполнение запроса на соответствие условию с использованием оператора равенства ==](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L279-183) | CosmosContainer.queryItems |
| [Выполнение запроса на несоответствие условию с использованием операторов != и NOT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L285-L293) | CosmosContainer.queryItems |
| [Выполнение запроса с использованием операторов диапазона, таких как >, <, >=, <=](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L295-L300) | CosmosContainer.queryItems |
| [Выполнение запроса к строкам с использованием операторов диапазона](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L302-L307) | CosmosContainer.queryItems |
| [Выполнение запроса с использованием предложения ORDER BY](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L309-L314) | CosmosContainer.queryItems |
| [Создание запросов с помощью DISTINCT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L316-L321) | CosmosContainer.queryItems |
| [Выполнение запроса с использованием агрегатных функций](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L323-L331) | CosmosContainer.queryItems |
| [Работа с вложенными документами](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L333-L341) | CosmosContainer.queryItems |
| [Выполнение запроса с использованием внутридокументных соединений](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L343-L365) | CosmosContainer.queryItems |
| [Выполнение запроса с использованием операторов string, math и array](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L367-L378) | CosmosContainer.queryItems |
| [Выполнение параметризованного SQL-запроса с использованием SqlQuerySpec](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L380-L409) |CosmosContainer.queryItems |
| [Выполнение запроса с применением явного разбиения на страницы](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L200-L254) | CosmosContainer.queryItems |
| [Выполнение запроса к секционированным коллекциям в параллельном режиме](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L256-L277) | CosmosContainer.queryItems |
| Выполнение запроса к секционированным коллекциям с использованием предложения ORDER BY | CosmosContainer.queryItems |

## <a name="change-feed-examples"></a>Примеры веб-канала изменений 
В файле [Change Feed Processor Sample](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java) показано, как выполнять следующие задачи. Чтобы узнать больше о канале изменений в Azure Cosmos DB перед выполнением приведенных ниже примеров, ознакомьтесь со статьями [Чтение канала изменений Azure Cosmos DB](read-change-feed.md) и [Обработчик канала изменений в Azure Cosmos DB](change-feed-processor.md).

| Задача | Справочник по API |
| --- | --- |
| [Базовая функциональность канала изменений](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L50-L120) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| Чтение канала изменений за определенное время | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [Чтение канала изменений с начала](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L50-L120) | - |

## <a name="server-side-programming-examples"></a>Примеры программирования на стороне сервера

В файле [Stored Procedure Sample](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java) показано, как выполнять следующие задачи. Чтобы узнать больше о программировании на стороне сервера в Azure Cosmos DB перед выполнением приведенных ниже примеров, ознакомьтесь со статьей о [хранимых процедурах, триггерах и определяемых пользователем функциях](stored-procedures-triggers-udfs.md).

| Задача | Справочник по API |
| --- | --- |
| [Создание хранимой процедуры](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L124-L143) | CosmosScripts.createStoredProcedure |
| [Выполнение хранимой процедуры](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L160-L174) | CosmosStoredProcedure.execute |
| [Удаление хранимой процедуры](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L176-L186) | CosmosStoredProcedure.delete |

## <a name="user-management-examples"></a>Примеры управления пользователями
В файле User Management Sample показано, как выполнять следующие задачи.

| Задача | Справочник по API |
| --- | --- |
| Создание пользователя | - |
| Установка разрешений на коллекцию или документ | - |
| Получение списка разрешений пользователя |- |