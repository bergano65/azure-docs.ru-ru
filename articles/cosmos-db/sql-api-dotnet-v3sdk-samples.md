---
title: 'Azure Cosmos DB: примеры .NET (Microsoft.Azure.Cosmos) для API SQL'
description: Найдите примеры C# пакета SDK для .NET V3 на сайте GitHub для распространенных задач с помощью API SQL для Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2019
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 37ecca6829f348137bbbc8b76fb1934c024550fd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097266"
---
# <a name="azure-cosmos-dbnet-v3-sdk-microsoftazurecosmos-examples-for-the-sql-api"></a>Azure Cosmos DB: примеры SDK .NET V3 (Microsoft.Azure.Cosmos) для API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Примеры пакета SDK для .NET версии 2](sql-api-dotnet-samples.md)
> * [Примеры пакета SDK для .NET версии 3](sql-api-dotnet-v3sdk-samples.md)
> * [Примеры для пакета SDK Java версии 4](sql-api-java-sdk-samples.md)
> * [Примеры для пакета SDK для Spring Data версии 3](sql-api-spring-data-sdk-samples.md)
> * [Примеры Node.js](sql-api-nodejs-samples.md)
> * [Примеры Python](sql-api-python-samples.md)
> * [Коллекция примеров кода Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
>
>

Новейшие примеры решений, которые выполняют операции CRUD и другие распространенные операции с ресурсами Azure Cosmos DB, доступны в репозитории GitHub [azure-cosmos-dotnet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage). Если вы уже знакомы с предыдущей версией пакета SDK для .NET, возможно термины коллекция и документ не будут для вас в новинку. Так как Azure Cosmos DB поддерживает несколько моделей API, в версии 3.0 пакета SDK для .NET используются стандартные термины "контейнер" и "элемент". Контейнер может представлять собой коллекцию, граф или таблицу. Элемент может представлять собой документ, ребро, вершину или запись и является содержимым внутри контейнера. Эта статья содержит:

* Ссылки на задачи в каждом из примеров файлов проектов C#.
* Ссылки на соответствующие справочные материалы по API.

## <a name="prerequisites"></a>Предварительные требования

Visual Studio 2019 с установленной рабочей нагрузкой разработки для Azure

- Вы можете скачать и использовать **бесплатную** версию [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность **разработки для Azure**.

   [Пакет NuGet Microsoft.Azure.cosmos](https://www.nuget.org/packages/Microsoft.Azure.cosmos/)

Подписка Azure или бесплатная пробная учетная запись Cosmos DB.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Вы можете [активировать преимущества подписчика Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): в вашей подписке Visual Studio каждый месяц зачисляются деньги на счет, которые можно использовать для оплаты служб Azure.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> Образцы являются автономными и настраиваются и удаляются после использования. За выполнение одного примера взимается плата за один час использования уровня производительности вашего контейнера.
> 

## <a name="database-examples"></a>Примеры баз данных

Метод [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L65-L91) из примера проекта *DatabaseManagement* демонстрирует, как выполнять следующие задачи. Чтобы узнать больше о базах данных Azure Cosmos перед выполнением приведенных ниже примеров, ознакомьтесь со статьей о [работе с базами данных, контейнерами и элементами](account-databases-containers-items.md).

| Задача | Справочник по API |
| --- | --- |
| [Создание базы данных](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L68) |[CosmosClient.CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet&preserve-view=true) |
| [Чтение базы данных по идентификатору](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L80) |[Database.ReadAsync](/dotnet/api/microsoft.azure.cosmos.database.readasync?view=azure-dotnet&preserve-view=true) |
| [Чтение всех баз данных для учетной записи](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L96) |[CosmosClient.GetDatabaseQueryIterator](/dotnet/api/microsoft.azure.cosmos.cosmosclient.getdatabasequeryiterator?view=azure-dotnet&preserve-view=true) |
| [Удаление базы данных](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L106) |[Database.DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet&preserve-view=true) |

## <a name="container-examples"></a>Примеры контейнеров

Метод [RunContainerDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L69-L89) в примере проекта *ContainerManagement* показывает, как выполнять следующие задачи. Чтобы узнать больше о контейнерах Azure Cosmos перед выполнением приведенных ниже примеров, ознакомьтесь со статьей о [работе с базами данных, контейнерами и элементами](account-databases-containers-items.md).

| Задача | Справочник по API |
| --- | --- |
| [Создание контейнера](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L97-L107) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet&preserve-view=true) |
| [Создание контейнера с настраиваемой политикой индексирования](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L161-L178) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet&preserve-view=true) |
| [Изменение настроенной производительности контейнера](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L198-L223) |[Container.ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet&preserve-view=true) |
| [Получение контейнера по идентификатору](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L225-L236) |[Container.ReadContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.readcontainerasync?preserve-view=true&view=azure-dotnet) |
| [Чтение всех контейнеров в базе данных](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L242-L258) |[Database.GetContainerQueryIterator](/dotnet/api/microsoft.azure.cosmos.database.getcontainerqueryiterator?view=azure-dotnet&preserve-view=true) |
| [Удаление контейнера](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L264-L270) |[Container.DeleteContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.deletecontainerasync?view=azure-dotnet&preserve-view=true) |

## <a name="item-examples"></a>Примеры элементов

Метод [RunItemsDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L119-L130) из примера проекта *ItemManagement* демонстрирует, как выполнять следующие задачи. Чтобы узнать больше об элементах Azure Cosmos перед выполнением приведенных ниже примеров, ознакомьтесь со статьей о [работе с базами данных, контейнерами и элементами](account-databases-containers-items.md).

| Задача | Справочник по API |
| --- | --- |
| [Создание элемента](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L161-L200) |[Container.CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet&preserve-view=true) |
| [Чтение элемента по идентификатору](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L203-L241) |[container.ReadItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet&preserve-view=true) |
| [Запрос элементов](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L244-L320) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet&preserve-view=true) |
| [Замена элемента](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L415-L456) |[container.ReplaceItemAsync](/dotnet/api/microsoft.azure.cosmos.container.replaceitemasync?view=azure-dotnet&preserve-view=true) |
| [Операция UPSERT элемента](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L458-L509) |[container.UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet&preserve-view=true) |
| [Удаление элемента](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L511-L522) |[container.DeleteItemAsync](/dotnet/api/microsoft.azure.cosmos.container.deleteitemasync?view=azure-dotnet&preserve-view=true) |
| [Замена элемента с помощью условной проверки ETag](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L682-L772) |[RequestOptions.IfMatchEtag](/dotnet/api/microsoft.azure.cosmos.requestoptions.ifmatchetag?view=azure-dotnet&preserve-view=true) |

## <a name="indexing-examples"></a>Примеры индексирования

Метод [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L108-L122) из примера проекта *IndexManagement* демонстрирует, как выполнять следующие задачи. Чтобы узнать больше об индексировании в Azure Cosmos DB перед выполнением приведенных ниже примеров, ознакомьтесь со статьями о [политиках индексирования](index-policy.md), [типах индексирования](index-overview.md#index-kinds) и [путях индексирования](index-policy.md#include-exclude-paths). 

| Задача | Справочник по API |
| --- | --- |
| [Исключение элемента из индекса](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L130-L186) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.cosmos.indexingdirective?view=azure-dotnet&preserve-view=true) |
| [Использование отложенного индексирования](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L198-L220) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.indexingmode?view=azure-dotnet&preserve-view=true) |
| [Исключение указанных путей к элементам из индекса](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L230-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.excludedpaths?view=azure-dotnet&preserve-view=true) |

## <a name="query-examples"></a>Примеры запросов

Метод [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L76-L96) примера проекта *Queries* демонстрирует, как выполнять следующие задачи с помощью грамматики SQL-запросов, поставщика LINQ с использованием запросом, а также Lambda. Чтобы узнать больше об SQL-запросах в Azure Cosmos DB перед выполнением приведенных ниже примеров, ознакомьтесь со статьей о [примерах SQL-запросов для Azure Cosmos DB](./sql-query-getting-started.md).

| Задача | Справочник по API |
| --- | --- |
| [Запрос элементов из одного раздела](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L154-L186) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet&preserve-view=true) |
| [Запрос элементов из нескольких разделов](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L215-L275) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet&preserve-view=true) |
| [Запрос с помощью инструкции SQL](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L189-L212) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet&preserve-view=true) |

## <a name="change-feed-examples"></a>Примеры веб-канала изменений

Метод [RunBasicChangeFeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) из примера проекта *ChangeFeed* демонстрирует, как выполнять следующие задачи. Чтобы узнать больше о канале изменений в Azure Cosmos DB перед выполнением приведенных ниже примеров, ознакомьтесь со статьями [Чтение канала изменений Azure Cosmos DB](read-change-feed.md) и [Обработчик канала изменений в Azure Cosmos DB](change-feed-processor.md).

| Задача | Справочник по API |
| --- | --- |
| [Базовая функциональность канала изменений](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet&preserve-view=true) |
| [Чтение канала изменений за определенное время](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L127-L162) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet&preserve-view=true) |
| [Чтение канала изменений с начала](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L170-L198) |[ChangeFeedProcessorBuilder.WithStartTime(DateTime)](/dotnet/api/microsoft.azure.cosmos.changefeedprocessorbuilder.withstarttime?view=azure-dotnet&preserve-view=true) |
| [Переход с обработчика канала изменений на канал изменений в пакете SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L256-L333) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet&preserve-view=true) |

## <a name="server-side-programming-examples"></a>Примеры программирования на стороне сервера

Метод [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L72-L102) из примера проекта *ServerSideScripts* демонстрирует, как выполнять следующие задачи. Чтобы узнать больше о программировании на стороне сервера в Azure Cosmos DB перед выполнением приведенных ниже примеров, ознакомьтесь со статьей о [хранимых процедурах, триггерах и определяемых пользователем функциях](stored-procedures-triggers-udfs.md).

| Задача | Справочник по API |
| --- | --- |
| [Создание хранимой процедуры](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L116) |[Scripts.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.createstoredprocedureasync?view=azure-dotnet&preserve-view=true) |
| [Выполнение хранимой процедуры](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L135) |[Scripts.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.executestoredprocedureasync?view=azure-dotnet&preserve-view=true) |
| [Удаление хранимой процедуры](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L351) |[Scripts.DeleteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.deletestoredprocedureasync?view=azure-dotnet&preserve-view=true) |
