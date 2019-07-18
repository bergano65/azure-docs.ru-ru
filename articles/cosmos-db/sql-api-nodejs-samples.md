---
title: Примеры Node.js для Azure Cosmos DB
description: Примеры Node.js на сайте GitHub, позволяющие выполнять общие задачи в Azure Cosmos DB, в том числе операции CRUD.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: d6d3de435537e5e55783f69dfd1dde0fbf8cb26e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806441"
---
# <a name="azure-cosmos-db-nodejs-examples"></a>Примеры Node.js для Azure Cosmos DB
> [!div class="op_single_selector"]
> * [Примеры .NET](sql-api-dotnet-samples.md)
> * [Примеры Java](sql-api-java-samples.md)
> * [Примеры Async Java](sql-api-async-java-samples.md)
> * [Примеры Node.js](sql-api-nodejs-samples.md)
> * [Примеры Python](sql-api-python-samples.md)
> * [Коллекция примеров кода Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Примеры решений, которые выполняют операции CRUD и другие распространенные операции с ресурсами Azure Cosmos DB, содержатся в репозитории GitHub [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples). Эта статья содержит:

* Ссылки на задачи в каждом из примеров файлов проектов Node.js.
* Ссылки на соответствующие справочные материалы по API.

**Предварительные требования**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Вы можете [активировать преимущества подписчика Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): в вашей подписке Visual Studio каждый месяц зачисляются деньги на счет, которые можно использовать для оплаты служб Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Вам также нужен [пакет SDK для JavaScript](sql-api-sdk-node.md).
   
   > [!NOTE]
   > Каждый пример является самодостаточным, он устанавливается самостоятельно и выполняет необходимую очистку после удаления. Поэтому примеры выполняют несколько вызовов метода [Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest). Каждый раз, когда это происходит, вам будет выставляться счет за 1 час использования каждого уровня производительности создаваемого контейнера.
   > 
   > 

## <a name="database-examples"></a>Примеры баз данных
С помощью файла [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement/app.js) в проекте [DatabaseManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/DatabaseManagement) вы узнаете, как выполнять следующие задачи. Чтобы узнать больше о базах данных Azure Cosmos перед выполнением приведенных ниже примеров, ознакомьтесь с тематической статьей [Работа с базами данных, контейнерами и элементами](databases-containers-items.md). 

| Задача | Справочник по API |
| --- | --- |
| [Создание базы данных при ее отсутствии](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L35-L37) |[Databases.createIfNotExists](https://docs.microsoft.com/javascript/api/%40azure/cosmos/databases?view=azure-node-latest) |
| [Получение списка баз данных для учетной записи](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L40-L42) |[Databases.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/databases?view=azure-node-latest) |
| [Чтение базы данных по идентификатору](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L40-L42) |[Database.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/database?view=azure-node-latest) |
| [Удаление базы данных](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L57-L60) |[Database.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/database?view=azure-node-latest) |

## <a name="container-examples"></a>Примеры контейнеров
С помощью файла [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement/app.js) в проекте [ContainerManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) вы узнаете, как выполнять следующие задачи. Чтобы узнать больше о коллекциях Azure Cosmos перед выполнением приведенных ниже примеров, ознакомьтесь с тематической статьей [Работа с базами данных, контейнерами и элементами](databases-containers-items.md). 

| Задача | Справочник по API |
| --- | --- |
| [Создание контейнера при его отсутствии](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L36-L37) |[Containers.createIfNotExists](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest) |
| [Получение списка контейнеров учетной записи](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L36-L37) |[Containers.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest) |
| [Чтение коллекции по идентификатору](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L47-L51) |[Container.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/container?view=azure-node-latest) |
| [Удаление контейнера](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L54-L55) |[Container.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/container?view=azure-node-latest) |

## <a name="item-examples"></a>Примеры элементов
С помощью файла [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement/app.js) в проекте [ItemManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) вы узнаете, как выполнять следующие задачи. Чтобы узнать больше о документах Azure Cosmos перед выполнением приведенных ниже примеров, ознакомьтесь с тематической статьей [Работа с базами данных, контейнерами и элементами](databases-containers-items.md). 

| Задача | Справочник по API |
| --- | --- |
| [Создание элементов](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L49-L56) |[Items.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Чтение всех элементов в контейнере](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L59-L64) |[Items.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Чтение элемента по идентификатору](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L59-L64) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |
| [Чтение элемента, только если он изменен](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L73-L94) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Запрос документов](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L97-L118) |[Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Замена элемента](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L131-L136) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |
| [Замена элемента с помощью условной проверки ETag](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L139-L160) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Удаление элемента](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L162-L164) |[Item.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |

## <a name="indexing-examples"></a>Примеры индексирования
С помощью файла [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement/app.js) в проекте [IndexManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/IndexManagement) вы узнаете, как выполнять следующие задачи.  Чтобы узнать больше об индексировании в Azure Cosmos DB перед выполнением приведенных ниже примеров, ознакомьтесь с тематическими статьями, посвященными [политикам](index-policy.md), [типам](index-types.md) и [путям индексирования](index-paths.md). 

| Задача | Справочник по API |
| --- | --- |
| [Индексирование определенного документа вручную](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L135-L177) |[RequestOptions.indexingDirective: 'include'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Исключение определенного документа из индекса вручную](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L90-L131) |[RequestOptions.indexingDirective: 'exclude'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Использование отложенного индексирования для массового импорта или чтения больших контейнеров](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L183-L214) |[IndexingMode.Lazy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingmode?view=azure-node-latest) |
| [Исключение пути из индекса](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L352-L429) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest#excludedpaths) |
| [Разрешение проверки строкового пути во время операции диапазона](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L219-L275) |[FeedOptions.EnableScanInQuery](https://docs.microsoft.com/javascript/api/%40azure/cosmos/feedoptions?view=azure-node-latest#enablescaninquery) |
| [Создание индекса диапазона строкового пути](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L281-L346) |[IndexKind.Range](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexkind?view=azure-node-latest), [IndexingPolicy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest), [Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Создание контейнера с политикой indexPolicy по умолчанию, а затем ее обновление через Интернет](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L435-L507) |[Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest)

## <a name="server-side-programming-examples"></a>Примеры программирования на стороне сервера
С помощью файла [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/app.js) в проекте [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) вы узнаете, как выполнять следующие задачи. Чтобы узнать больше о программировании на стороне сервера в Azure Cosmos DB перед выполнением приведенных ниже примеров, ознакомьтесь с тематической статьей [Хранимые процедуры, триггеры и определяемые пользователем функции](stored-procedures-triggers-udfs.md). 

| Задача | Справочник по API |
| --- | --- |
| [Создание хранимой процедуры](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ServerSideScripts/JS/upsert.js#L12-L72) |[StoredProcedures.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedures?view=azure-node-latest) |
| [Выполнение хранимой процедуры](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ServerSideScripts/app.js#L44) |[StoredProcedure.execute](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedure?view=azure-node-latest) |

Дополнительные сведения о программировании на стороне сервера в Azure Cosmos DB, в том числе о хранимых процедурах, триггерах баз данных и определяемых пользователем функциях, см. в [этой статье](stored-procedures-triggers-udfs.md).

