---
title: Azure Cosmos DB — Примеры Async Java для API SQL
description: Примеры Async Java на сайте GitHub, позволяющие выполнять распространенные задачи с использованием API SQL для Azure Cosmos DB, включая операции CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: sample
ms.date: 06/18/2018
ms.author: sngun
ms.openlocfilehash: 34551dcc64e6add2baa1ec94067037c98de67abe
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58917946"
---
# <a name="azure-cosmos-db-async-java-examples-for-the-sql-api"></a>Azure Cosmos DB — Примеры Async Java для API SQL

> [!div class="op_single_selector"]
> * [Примеры .NET](sql-api-dotnet-samples.md)
> * [Примеры Java](sql-api-java-samples.md)
> * [Примеры Async Java](sql-api-async-java-samples.md)
> * [Примеры Node.js](sql-api-nodejs-samples.md)
> * [Примеры Python](sql-api-python-samples.md)
> * [Коллекция примеров кода Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Новейшие примеры приложений, которые выполняют операции CRUD и другие распространенные операции с ресурсами Azure Cosmos DB, содержатся в репозитории GitHub [azure-cosmosdb-java](https://github.com/Azure/azure-cosmosdb-java). Эта статья содержит:

* ссылки на задачи в каждом из примеров файлов проектов Async Java; 
* Ссылки на соответствующие справочные материалы по API.

**Предварительные требования**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Вы можете [активировать преимущества подписчика Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): в вашей подписке Visual Studio каждый месяц зачисляются деньги на счет, которые можно использовать для оплаты служб Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Чтобы запустить пример приложения, необходимы следующие компоненты:

* комплект разработчика Java 8;
* пакет Java SDK для Microsoft Azure Cosmos DB.

При необходимости можно использовать Maven, чтобы получить последние двоичные файлы пакета Java SDK для Microsoft Azure Cosmos DB для использования в своем проекте. Последнюю версию можно скачать [здесь](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb). Maven автоматически добавляет все необходимые зависимости. В противном случае можно непосредственно скачать зависимости, которые перечислены в файле pom.xml, и добавить их в путь сборки.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-cosmosdb</artifactId>
    <version>${LATEST}</version>
</dependency>
```

**Запуск примеров приложений**

Клонирование примера репозитория:
```bash
$ git clone https://github.com/Azure/azure-cosmosdb-java.git

$ cd azure-cosmosdb-java
```

Примеры можно запустить, используя Eclipse, или с помощью командной строки, используя Maven.

Для запуска из Eclipse:
* Загрузите файл pom.xml основного родительского проекта в Eclipse. Примеры azure-cosmosdb-examples должны загрузиться автоматически.
* Для запуска примеров требуется допустимая конечная точка Azure Cosmos DB. Конечные точки будут считаны из `src/test/java/com/microsoft/azure/cosmosdb/rx/examples/TestConfigurations.java`.
* Учетные данные конечной точки можно передать в качестве аргументов виртуальной машины в конфигурацию выполнения JUnit Eclipse или поместить их в файл TestConfigurations.java.
    ```bash
    -DACCOUNT_HOST=<Fill your Azure Cosmos DB account name> -DACCOUNT_KEY=<Fill your Azure Cosmos DB primary key>
    ```
* Теперь можно выполнить примеры в качестве тестов JUnit в Eclipse.

Для запуска из командной строки:
* Другой способ запуска примеров — использование Maven:
* Запустите Maven и передайте учетные данные конечной точки Azure Cosmos DB:
    ```bash
    mvn test -DACCOUNT_HOST=<Fill your Azure Cosmos DB account name> -DACCOUNT_KEY=<Fill your Azure Cosmos DB primary key>
    ```

   > [!NOTE]
   > Каждый пример является самодостаточным, он устанавливается самостоятельно и выполняет необходимую очистку после удаления. Примеры выполняют множественные вызовы метода [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection). Каждый раз, когда это происходит, вам выставляется счет за 1 час использования каждого уровня производительности создаваемой коллекции. 
   > 
   > 

## <a name="database-examples"></a>Примеры баз данных
В файле [DatabaseCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java) показано, как выполнять следующие задачи. Чтобы узнать больше о базах данных Azure Cosmos перед выполнением приведенных ниже примеров, ознакомьтесь с тематической статьей [Работа с базами данных, контейнерами и элементами](databases-containers-items.md). 

| Задача | Справочник по API |
| --- | --- |
| [Создание базы данных](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L115-L132) | [AsyncDocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createdatabase) |
| [Не удалось создать базу данных, которая уже существует](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L189-L204) | |
| [Создание и чтение базы данных](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L228-L249) | [AsyncDocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readdatabase) |
| [Создание и удаление базы данных](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L255-L276) | [AsyncDocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletedatabase) |
| [Создание и запрос базы данных](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L282-L312) | [AsyncDocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydatabases) |

## <a name="collection-examples"></a>Примеры коллекций
В файле [CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) показано, как выполнять следующие задачи. Чтобы узнать больше о коллекциях Azure Cosmos перед выполнением приведенных ниже примеров, ознакомьтесь с тематической статьей [Работа с базами данных, контейнерами и элементами](databases-containers-items.md). 

| Задача | Справочник по API |
| --- | --- |
| [Создание односекционной коллекции](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L134-L153) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection) |
| [Создание пользовательской многосекционной коллекции](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L164-L184) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.partitionkeydefinition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.requestoptions) |
| [Не удалось создать коллекцию, которая уже существует](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L241-L256) | |
| [Создание и чтение коллекции](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L281-L304) | [AsyncDocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readcollection) |
| [Создание и удаление коллекции](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L310-L333) | [AsyncDocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletecollection) |
| [Создание коллекции и запросов к ней](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L339-L372) | [AsyncDocumentClient.queryCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querycollections) |

## <a name="document-examples"></a>Примеры документов
В файле [DocumentCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java) показано, как выполнять следующие задачи. Чтобы узнать больше о документах Azure Cosmos перед выполнением приведенных ниже примеров, ознакомьтесь с тематической статьей [Работа с базами данных, контейнерами и элементами](databases-containers-items.md). 

| Задача | Справочник по API |
| --- | --- |
| [Создание документа](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L137-L156) | [AsyncDocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createdocument) |
| [Создание документа с программируемым определением документа](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L213-L242) | [Документ](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.resource.setid) |
| [Создание документов и определение общей стоимости единиц запроса](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L248-L280) | [ResourceResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.resourceresponse.getrequestcharge) |
| [Не удалось создать документ, который уже существует](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L316-L336) | |
| [Создание и замена документа](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L342-L365) | [AsyncDocumentClient.replaceDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.replacedocument) |
| [Создание и вставка документа](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L371-L393) | [AsyncDocumentClient.upsertDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.upsertdocument) |
| [Создание и удаление документа](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L399-L431) | [AsyncDocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletedocument) |
| [Создание и чтение документа](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L437-L458) | [AsyncDocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readdocument) |

## <a name="indexing-examples"></a>Примеры индексирования
В файле [CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) показано, как выполнять следующие задачи.  Чтобы узнать больше об индексировании в Azure Cosmos DB перед выполнением приведенных ниже примеров, ознакомьтесь с тематическими статьями, посвященными [политикам](index-policy.md), [типам](index-types.md) и [путям индексирования](index-paths.md). 

| Задача | Справочник по API |
| --- | --- |
| [Создание индекса и установка политики индексирования](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L394-L410) | [Индекс](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.indexingpolicy) |

Дополнительные сведения об индексации Azure Cosmos DB см. в [этой статье](index-policy.md).

## <a name="query-examples"></a>Примеры запросов
В файле [DocumentQuerySamples](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java) показано, как выполнять следующие задачи. Чтобы узнать больше об SQL-запросах в Azure Cosmos DB перед выполнением приведенных ниже примеров, ознакомьтесь с [этой тематической статьей](how-to-sql-query.md). 

| Задача | Справочник по API |
| --- | --- |
| [Выполнение простого запроса к документу](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L154-L190) | [AsyncDocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydocuments) |
| [Выполнение простого запроса к документу и определение общей стоимости единиц запроса](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L249-L268) | [FeedResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.feedresponse.getrequestcharge) |
| [Выполнение простого запроса к документу, чтение одной страницы и отмена подписки на возвращаемый наблюдаемый объект](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L274-L312) | |
| [Выполнение простого запроса к документу и фильтрация результатов](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L318-L368) | |
| [Выполнение межсекционного запроса orderBy к документу](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L410-L457) | [FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.feedoptions.setenablecrosspartitionquery) |

Дополнительные сведения о создании запросов см. в статье [SQL-запрос в Azure Cosmos DB](how-to-sql-query.md).

## <a name="offer-examples"></a>Примеры предложения
В файле [OfferCRUDAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java) показано, как выполнять следующие задачи:

| Задача | Справочник по API |
| --- | --- |
| [Создание коллекции и настройка пропускной способности](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L106-L113) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection)<br>[RequestOptions.setOfferThroughput](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.requestoptions.setofferthroughput) |
| [Чтение коллекции для поиска связанных предложений](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L118-L130) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.offer.getContent)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.queryoffers) |
| [Изменение пропускной способности коллекции путем замены соответствующего предложения](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L101-L153) | [AsyncDocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.replaceoffer) |

## <a name="stored-procedure-examples"></a>Примеры хранимой процедуры
В файле [StoredProcedureAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java) показано, как выполнять следующие задачи. Чтобы узнать больше о программировании на стороне сервера в Azure Cosmos DB перед выполнением приведенных ниже примеров, ознакомьтесь с тематической статьей [Хранимые процедуры, триггеры и определяемые пользователем функции](stored-procedures-triggers-udfs.md). 

| Задача | Справочник по API |
| --- | --- |
| [Создание и выполнение хранимой процедуры](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L108-L155) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.storedprocedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createstoredprocedure)<br>[AsyncDocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.executestoredprocedure) |
| [Создание и выполнение хранимой процедуры с использованием аргументов](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L161-L195) | |
| [Создание и выполнение хранимой процедуры с использованием аргумента объекта](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L201-L241) | |

## <a name="unique-key"></a>Уникальный ключ
В файле [UniqueIndexAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java) показано, как выполнять следующие задачи. Чтобы узнать больше об уникальных ключах в Azure Cosmos DB перед выполнением приведенных ниже примеров, ознакомьтесь с [этой тематической статьей](unique-keys.md). 

| Задача | Справочник по API |
| --- | --- |
| [Создание коллекции с уникальным ключом](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java#L65-L97) | [UniqueKey](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.uniquekey)<br>[UniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.uniquekeypolicy)<br>[DocumentCollection.setUniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection.setuniquekeypolicy) |
