---
title: Azure Cosmos DB — API-интерфейс, пакет SDK и ресурсы .NET для библиотеки Bulk Executor
description: Сведения об API и пакетах SDK .NET для библиотеки Bulk Executor, в том числе даты выхода, даты снятия с учета и изменения, внесенные в каждую версию пакета SDK .NET для Bulk Executor в Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/19/2018
ms.author: ramkris
ms.openlocfilehash: 74eddadd7fd967daa1eebb9d7cb223fdc708025f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66471422"
---
# <a name="net-bulk-executor-library-download-information"></a>Библиотека Bulk Executor для .NET: информация о скачивании 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Веб-канал изменений в .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Поставщик ресурсов REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk Executor — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor — Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Описание**| Библиотека Bulk Executor позволяет клиентским приложениям выполнять массовые операции в учетных записях Azure Cosmos DB. Библиотека Bulk Executor предоставляет пространства имен BulkImport, BulkUpdate и BulkDelete. Модуль BulkImport может оптимизировать массовый прием документов, обеспечивая использование максимального объема пропускной способности, подготовленной для коллекции. Модуль BulkUpdate позволяет выполнять массовое обновление существующих данных в контейнерах Azure Cosmos DB в качестве исправлений. Модуль BulkDelete может оптимизировать массовое удаление документов, обеспечивая использование максимального объема пропускной способности, подготовленной для коллекции.|
|**Скачивание пакета SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Библиотека BulkExecutor в GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Документация по API**|[Справочная документация по API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Начало работы**|[Начало работы с пакетом SDK для .NET для библиотеки Bulk Executor](bulk-executor-dot-net.md)|
| **Текущая поддерживаемая платформа**| Microsoft .NET Framework 4.5.2, 4.6.1 и .NET Standard 2.0 |

## <a name="release-notes"></a>Заметки о выпуске

### <a name="a-name230-preview2230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* Добавлена поддержка для исполнителя массового graph для принятия ttl на вершины и ребра

### <a name="a-name220-preview2220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-preview2

* Устранена проблема, которая вызвали исключения во время гибкое масштабирование Azure Cosmos DB при работе в режиме шлюза. Это исправление позволяет функционально эквивалентен 1.4.1 выпуска.

### <a name="a-name210-preview2210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-preview2:

* Добавлена массового удаления поддержки учетных записей SQL API для приема ключ секции, кортежи идентификатор документа для удаления. Это изменение делает функционально эквивалентен 1.4.0 выпуска.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Включено MongoBulkExecutor для поддержки .NET Standard 2.0. Благодаря этой возможности выпуск функционально эквивалентен выпуску 1.3.0 с добавлением поддержки .NET Standard 2.0 в качестве целевой платформы.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* Добавлена .NET Standard 2.0 в качестве одной из поддерживаемых целевых платформ, чтобы библиотека BulkExecutor работала с приложениями .NET Core.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Обновить массового исполнителя, чтобы использовать последнюю версию Azure Cosmos DB .NET SDK (версии 2.4.0)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0

* Добавлена поддержка для исполнителя массового graph для принятия ttl на вершины и ребра

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

* Устранена проблема, которая вызвали исключения во время гибкое масштабирование Azure Cosmos DB при работе в режиме шлюза.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

* Добавлена массового удаления поддержки учетных записей SQL API для приема ключ секции, кортежи идентификатор документа для удаления.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* Устранена проблема, вызывавшая проблему форматирования в агенте пользователя, используемом в BulkExecutor.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Внесены улучшения в импорт BulkExecutor и API-интерфейсы обновления для прозрачной адаптации к гибкому масштабированию контейнера Cosmos DB при превышении текущей емкости хранилища без вызова исключений.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Зависимость пакета SDK для .NET DocumentDB обновлена до версии 2.1.3.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Устранена проблема, при которой библиотека BulkExecutor выдавала ошибку JSRT во время импорта в фиксированные коллекции.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Добавлена поддержка операции BulkDelete для учетных записей API Azure Cosmos DB SQL.
* Добавлена поддержка операции BulkImport для учетных записей API Azure Cosmos DB для MongoDB.
* Зависимость пакета SDK для .NET DocumentDB обновлена до версии 2.0.0. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Добавлена поддержка операции BulkImport для учетных записей API Azure Cosmos DB Gremlin.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Исправлена незначительная ошибка операции BulkImport для учетных записей API Azure Cosmos DB SQL.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Добавлена поддержка операции BulkImport и BulkUpdate для учетных записей API Azure Cosmos DB SQL.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о библиотеке Java Bulk Executor см. в следующей статье:

[Библиотека массового исполнителя Java — информация о скачивании](sql-api-sdk-bulk-executor-java.md)
