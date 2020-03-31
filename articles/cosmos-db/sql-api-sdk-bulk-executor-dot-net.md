---
title: 'Azure Космос DB: Массовый исполнитель .NET API, SDK & ресурсы'
description: Сведения об API и пакетах SDK .NET для библиотеки BulkExecutor, в том числе даты выхода, даты снятия с учета и изменения, внесенные в каждую версию пакета SDK .NET для BulkExecutor в Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/16/2020
ms.author: ramkris
ms.openlocfilehash: 1a8040fc397b526b540ce9343baa985cab49e2b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169395"
---
# <a name="net-bulk-executor-library-download-information"></a>Библиотека массового исполнителя .NET — информация о скачивании 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Лента изменения .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [Остальные](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Поставщик ресурсов REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Массовый исполнитель - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Массовый исполнитель - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Описание**| Библиотека-исполнитель .Net позволяет клиентским приложениям выполнять оптовые операции на учетных записях Azure Cosmos DB. Эта библиотека предоставляет пространства имен BulkImport, BulkUpdate и BulkDelete. Модуль BulkImport может оптимизировать массовый прием документов, обеспечивая использование максимального объема пропускной способности, подготовленной для коллекции. Модуль BulkUpdate может обыкновение обновлять существующие данные в контейнерах Azure Cosmos в виде патчей. Модуль BulkDelete может оптимизировать массовое удаление документов, обеспечивая использование максимального объема пропускной способности, подготовленной для коллекции.|
|**Загрузка SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Библиотека массового исполнителя в GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Документация API**|[Справочная документация по API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Начало работы**|[Начало работы с библиотекой-исполнителем .NET SDK](bulk-executor-dot-net.md)|
| **Текущая поддерживаемая платформа**| Microsoft .NET Framework 4.5.2, 4.6.1 и .NET Standard 2.0 |

> [!NOTE]
> Если вы используете оптовый исполнитель, пожалуйста, смотрите последнюю версию 3.x [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md), который имеет объемный исполнитель, встроенный в SDK. 

## <a name="release-notes"></a>Заметки о выпуске

### <a name="241-preview"></a><a name="2.4.1-preview"/>2.4.1-предварительный просмотр

* Исправлено TotalElapsedTime в ответbulkDelete правильно измерить общее время, включая любые повторы.

### <a name="240-preview"></a><a name="2.4.0-preview"/>2.4.0-предварительный просмотр

* Измененная зависимость SDK на >2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* Добавлена поддержка для исполнителя сыпучих графов для приема ttl на вершинах и краях

### <a name="220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-preview2

* Исправлена проблема, которая вызвала исключения при эластичном масштабировании Azure Cosmos DB при запуске в режиме Gateway. Это исправление делает его функционально эквивалентным 1.4.1 релиз.

### <a name="210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-preview2

* Добавлена поддержка BulkDelete для учетных записей API S'L, чтобы принять ключ раздела, tuples идокумента документа для удаления. Это изменение делает его функционально эквивалентным 1.4.0 релиз.

### <a name="200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Включено MongoBulkExecutor для поддержки .NET Standard 2.0. Благодаря этой возможности выпуск функционально эквивалентен выпуску 1.3.0 с добавлением поддержки .NET Standard 2.0 в качестве целевой платформы.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* Добавлен опорный стандарт .NET Standard 2.0 в качестве одной из поддерживаемых целевых платформ, чтобы библиотека-исполнитель собъемом работала с приложениями .NET Core.

### <a name="188"></a><a name="1.8.8"/>1.8.8

* Исправлена проблема на MongoBulkExecutor, которая неожиданно увеличивала размер документа, добавляя обивку, а в некоторых случаях и перекладывая максимальное ограничение размера документа.

### <a name="187"></a><a name="1.8.7"/>1.8.7

* Исправлена проблема с BulkDeleteAsync, когда коллекция вложена ключевых путей раздела.

### <a name="186"></a><a name="1.8.6"/>1.8.6

* MongoBulkExecutor теперь реализует IDisposable и, как ожидается, будет удален после использования.

### <a name="185"></a><a name="1.8.5"/>1.8.5

* Удален блокировка версии SDK. Пакет теперь зависит от SDK >2.5.1.

### <a name="184"></a><a name="1.8.4"/>1.8.4

* Фиксированная обработка идентификаторов при вызове BulkImport со списком объектов POCO с числовыми значениями.

### <a name="183"></a><a name="1.8.3"/>1.8.3

* Исправлено TotalElapsedTime в ответbulkDelete правильно измерить общее время, включая любые повторы.

### <a name="182"></a><a name="1.8.2"/>1.8.2

* Исправлено высокое потребление процессора в определенных сценариях.
* Трассировка теперь использует TraceSource. Пользователи могут определить `BulkExecutorTrace` слушателей для источника.
* Исправлен редкий сценарий, который может привести к блокировке при отправке документов около 2 Мб размера.

### <a name="160"></a><a name="1.6.0"/>1.6.0

* Обновлено основной исполнитель, чтобы теперь использовать последнюю версию Azure Космос DB .NET SDK (2.4.0)

### <a name="150"></a><a name="1.5.0"/>1.5.0

* Добавлена поддержка для исполнителя сыпучих графов для приема ttl на вершинах и краях

### <a name="141"></a><a name="1.4.1"/>1.4.1

* Исправлена проблема, которая вызвала исключения при эластичном масштабировании Azure Cosmos DB при запуске в режиме Gateway.

### <a name="140"></a><a name="1.4.0"/>1.4.0

* Добавлена поддержка BulkDelete для учетных записей API S'L, чтобы принять ключ раздела, tuples идокумента документа для удаления.

### <a name="130"></a><a name="1.3.0"/>1.3.0

* Исправлена проблема, которая вызвала проблему форматирования в агенте пользователя, используемом навалом исполнителя.

### <a name="120"></a><a name="1.2.0"/>1.2.0

* Сделано улучшение импорта навалом исполнителей и обновление AA, чтобы прозрачно адаптироваться к эластичной масштабированию контейнера Cosmos, когда хранение превышает текущую емкость без каких-либо исключений.

### <a name="112"></a><a name="1.1.2"/>1.1.2

* Зависимость пакета SDK для .NET DocumentDB обновлена до версии 2.1.3.

### <a name="111"></a><a name="1.1.1"/>1.1.1

* Исправлена проблема, которая привела к тому, что основной исполнитель бросил ошибку JSRT при импорте в фиксированные коллекции.

### <a name="110"></a><a name="1.1.0"/>1.1.0

* Добавлена поддержка операции BulkDelete для учетных записей API Azure Cosmos DB SQL.
* Добавлена поддержка операции BulkImport для учетных записей API Azure Cosmos DB для MongoDB.
* Зависимость пакета SDK для .NET DocumentDB обновлена до версии 2.0.0. 

### <a name="102"></a><a name="1.0.2"/>1.0.2

* Добавлена поддержка операции BulkImport для учетных записей API Azure Cosmos DB Gremlin.

### <a name="101"></a><a name="1.0.1"/>1.0.1

* Исправлена незначительная ошибка операции BulkImport для учетных записей API Azure Cosmos DB SQL.

### <a name="100"></a><a name="1.0.0"/>1.0.0

* Добавлена поддержка операции BulkImport и BulkUpdate для учетных записей API Azure Cosmos DB SQL.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать о массовом исполнителе Библиотеки Java, смотрите следующую статью:

[Java объемных исполнителей библиотеки SDK и релиз информации](sql-api-sdk-bulk-executor-java.md)
