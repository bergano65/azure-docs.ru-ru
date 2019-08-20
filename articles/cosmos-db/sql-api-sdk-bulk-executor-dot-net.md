---
title: Azure Cosmos DB — API .NET для пакетного исполнителя, ресурсы SDK &
description: Сведения об API и пакетах SDK .NET для библиотеки BulkExecutor, в том числе даты выхода, даты снятия с учета и изменения, внесенные в каждую версию пакета SDK .NET для BulkExecutor в Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/19/2018
ms.author: ramkris
ms.openlocfilehash: d24481c1364b41f38e4ca7b8f7d726ebc22a1117
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624647"
---
# <a name="net-bulk-executor-library-download-information"></a>Библиотека NET выполнителя .NET: информация о скачивании 

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
> * [Групповой исполнитель — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Групповой исполнитель — Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Описание**| Библиотека .NET NET выполнителя позволяет клиентским приложениям выполнять групповые операции с учетными записями Azure Cosmos DB. Эта библиотека предоставляет пространства имен Булкимпорт, Булкупдате и Булкделете. Модуль BulkImport может оптимизировать массовый прием документов, обеспечивая использование максимального объема пропускной способности, подготовленной для коллекции. Модуль Булкупдате может выполнять массовые обновления существующих данных в контейнерах Cosmos для Azure в качестве исправлений. Модуль BulkDelete может оптимизировать массовое удаление документов, обеспечивая использование максимального объема пропускной способности, подготовленной для коллекции.|
|**Скачивание пакета SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Библиотека с массовым исполнителем в GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Документация по API**|[Справочная документация по API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Начало работы**|[Начало работы с пакетом SDK для .NET для библиотеки массового исполнителя](bulk-executor-dot-net.md)|
| **Текущая поддерживаемая платформа**| Microsoft .NET Framework 4.5.2, 4.6.1 и .NET Standard 2.0 |

## <a name="release-notes"></a>Заметки о выпуске

### <a name="a-name230-preview2230-preview2"></a><a name="2.3.0-preview2"/>2.3.0 — preview2

* Добавлена поддержка многоисполнительного графа Graph для приема TTL на вершинах и краях.

### <a name="a-name220-preview2220-preview2"></a><a name="2.2.0-preview2"/>2.2.0 — preview2

* Исправлена проблема, которая привела к возникновению исключений во время эластичного масштабирования Azure Cosmos DB при работе в режиме шлюза. Это исправление функционально эквивалентно 1.4.1 выпуску.

### <a name="a-name210-preview2210-preview2"></a><a name="2.1.0-preview2"/>2.1.0 — preview2

* Добавлена поддержка Булкделете для учетных записей API SQL, чтобы принять ключ секции, кортежи идентификаторов документов для удаления. Это изменение делает его функционально эквивалентным выпуску 1.4.0.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Включено MongoBulkExecutor для поддержки .NET Standard 2.0. Благодаря этой возможности выпуск функционально эквивалентен выпуску 1.3.0 с добавлением поддержки .NET Standard 2.0 в качестве целевой платформы.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* Добавлена .NET Standard 2,0 в качестве одной из поддерживаемых целевых платформ, чтобы библиотека NET выполнителя работала с приложениями .NET Core.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Обновленный исполнитель теперь использует последнюю версию пакета SDK для Azure Cosmos DB .NET (2.4.0).

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0

* Добавлена поддержка многоисполнительного графа Graph для приема TTL на вершинах и краях.

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

* Исправлена проблема, которая привела к возникновению исключений во время эластичного масштабирования Azure Cosmos DB при работе в режиме шлюза.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

* Добавлена поддержка Булкделете для учетных записей API SQL, чтобы принять ключ секции, кортежи идентификаторов документов для удаления.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* Исправлена проблема, из-за которой в агенте пользователя, используемом массовым исполнителем, возникла проблема с форматированием.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Внесены улучшения в интерфейсные API импорта и обновления выполнителя, чтобы прозрачно адаптироваться к эластичному масштабированию контейнера Cosmos, когда хранилище превышает текущую емкость без возникновения исключений.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Зависимость пакета SDK для .NET DocumentDB обновлена до версии 2.1.3.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Исправлена проблема, которая привела к возникновению ошибки JSRT при импорте в фиксированные коллекции с помощью небольшого исполнителя.

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

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о библиотеке Java для неполного выполнителя см. в следующей статье:

[Пакет SDK и сведения о выпуске библиотеки пакетного выполнителя Java](sql-api-sdk-bulk-executor-java.md)
