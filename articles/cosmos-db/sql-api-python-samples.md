---
title: Примеры Python для API SQL в Azure Cosmos DB
description: Примеры Python на сайте GitHub, позволяющие выполнять общие задачи в Azure Cosmos DB, в том числе операции CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 05/20/2020
ms.author: sngun
ms.openlocfilehash: 3b92ae206aa623e948a612f31b48213421bf9da3
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798494"
---
# <a name="azure-cosmos-db-python-examples"></a>Примеры Python для Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Примеры пакета SDK для .NET версии 2](sql-api-dotnet-samples.md)
> * [Примеры пакета SDK для .NET версии 3](sql-api-dotnet-v3sdk-samples.md)
> * [Примеры пакета SDK для Java версии 4](sql-api-java-sdk-samples.md)
> * [Примеры Node.js](sql-api-nodejs-samples.md)
> * [Примеры Python](sql-api-python-samples.md)
> * [Коллекция примеров кода Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

Примеры решений, которые выполняют операции CRUD и другие распространенные операции с ресурсами Azure Cosmos DB, содержатся в репозитории GitHub [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python). Эта статья содержит:

* Ссылки на задачи в каждом из примеров файлов проектов Python.
* Ссылки на соответствующие справочные материалы по API.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Вы можете [активировать преимущества подписчика Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): в вашей подписке Visual Studio каждый месяц зачисляются деньги на счет, которые можно использовать для оплаты служб Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Вам также необходим [пакет SDK для Python](sql-api-sdk-python.md).

   > [!NOTE]
   > Каждый пример является самодостаточным, он устанавливается самостоятельно и выполняет необходимую очистку после удаления. В примерах выполняется несколько вызовов к `CosmosClient.CreateContainer`. При этом каждый раз на подписку выставляется счет за один час использования. Дополнительные сведения о выставлении счетов в Azure Cosmos DB см. на [странице цен на Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="database-examples"></a>Примеры баз данных

В примере [database_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) (Python) показано, как выполнять следующие задачи. Чтобы узнать больше о базах данных Azure Cosmos перед выполнением приведенных ниже примеров, ознакомьтесь с тематической статьей [Работа с базами данных, контейнерами и элементами](databases-containers-items.md).

| Задача | Справочник по API |
| --- | --- |
| [Создание базы данных](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient.create_database|
| [Чтение базы данных по идентификатору](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient.get_database_client|
| [Обращение к базам данных](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient.query_databases|
| [Получение списка баз данных для учетной записи](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient.list_databases|
| [Удаление базы данных](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient.delete_database|

## <a name="container-examples"></a>Примеры контейнеров

В примере [container_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) (Python) показано, как выполнять следующие задачи. Чтобы узнать больше о коллекциях Azure Cosmos перед выполнением приведенных ниже примеров, ознакомьтесь с тематической статьей [Работа с базами данных, контейнерами и элементами](databases-containers-items.md).

| Задача | Справочник по API |
| --- | --- |
| [Обращение к контейнеру](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |database.query_containers |
| [Создание контейнера](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |database.create_container |
| [Перечисление всех контейнеров в базе данных](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |database.list_containers |
| [Получение контейнера по идентификатору](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |database.get_container_client |
| [Управление подготовленной пропускной способностью контейнера](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |container.read_offer, container.replace_throughput|
| [Удаление контейнера](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |database.delete_container |

## <a name="item-examples"></a>Примеры элементов

В примере [item_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) (Python) показано, как выполнять следующие задачи. Чтобы узнать больше о документах Azure Cosmos перед выполнением приведенных ниже примеров, ознакомьтесь с тематической статьей [Работа с базами данных, контейнерами и элементами](databases-containers-items.md).

| Задача | Справочник по API |
| --- | --- |
| [Создание элементов в контейнере](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |container.create_item |
| [Считывание элемента по идентификатору](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |container.read_item |
| [Считывание всех элементов в контейнере](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |container.read_all_items |
| [Обращение к элементу по идентификатору](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |container.query_items |
| [Замена элемента](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |container.replace_items |
| [Операция UPSERT элемента](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |container.upsert_item |
| [Удаление элемента](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |container.delete_item |
| [Получение канала изменений элементов в контейнере](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |container.query_items_change_feed |

## <a name="indexing-examples"></a>Примеры индексирования

В примере [index_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) (Python) показано, как выполнять следующие задачи. Чтобы узнать больше об индексировании в Azure Cosmos DB перед выполнением приведенных ниже примеров, ознакомьтесь с тематическими статьями, посвященными [политикам](index-policy.md), [типам](index-types.md) и [путям индексирования](index-paths.md).

| Задача | Справочник по API |
| --- | --- |
| [Исключение определенного элемента из индексации](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [Использование ручного индексирования с определенными проиндексированными элементами](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | documents.IndexingDirective.Include |
| [Исключение путей из индексирования](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |Определение путей для исключения в свойстве `IndexingPolicy`. |
| [Использование диапазонных индексов в строках](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | Определение политики индексации с индексами диапазона для строкового типа данных. `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [Преобразование индекса](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |database.replace_container (use the updated indexing policy)|
| [Использование проверки, если в пути существует только хэш-индекс](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | Определение `enable_scan_in_query=True` и `enable_cross_partition_query=True` при обращении к элементам |