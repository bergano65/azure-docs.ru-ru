---
title: Azure Cosmos DB — API-интерфейс Java, пакет SDK и ресурсы для исполнителя массовых операций
description: Сведения о пакете SDK и API Java массового исполнителя, включая даты выхода и прекращения использования, а также изменения, внесенные в каждую версию пакета SDK Java массового исполнителя в Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: 6fe075d2679ec13217200bc8b0da132e8f4d9b57
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660443"
---
# <a name="java-bulk-executor-library-download-information"></a>Библиотека Bulk Executor для Java: информация о скачивании

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Веб-канал изменений в .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Пакет SDK для Java версии 4](sql-api-sdk-java-v4.md)
> * [Пакет SDK для Async Java версии 2](sql-api-sdk-async-java.md)
> * [Пакет SDK для Sync Java версии 2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Поставщик ресурсов REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Исполнитель массовых операций — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Исполнитель массовых операций — Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Описание**|Библиотека массового исполнителя позволяет клиентским приложениям выполнять массовые операции в учетных записях Azure Cosmos DB. Библиотека массового исполнителя предоставляет пространства имен BulkImport и BulkUpdate. Модуль BulkImport может оптимизировать массовый прием документов, обеспечивая использование максимального объема пропускной способности, подготовленной для коллекции. Модуль BulkUpdate позволяет выполнять массовое обновление существующих данных в контейнерах Azure Cosmos в виде исправлений.|
|**Скачивание пакета SDK**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Библиотека исполнителя массовых операций в GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Документация по API**| [Справочная документация по API Java](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Начало работы**|[Начало работы с пакетом SDK для Java для библиотеки массового исполнителя](bulk-executor-java.md)|
|**Минимальная поддерживаемая среда выполнения**|[Пакет средств разработки Java (JDK) 7+](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Заметки о выпуске

### <a name="2100"></a><a name="2.10.0"/>2.10.0

* Внесено исправление для DocumentAnalyzer.java, чтобы значения ключей вложенных разделов правильно извлекались из JSON.

### <a name="294"></a><a name="2.9.4"/>2.9.4

* Добавлены дополнительные функции BulkDelete для повторного выполнения операции в случае конкретных сбоев, а также для получения пользователем списка сбоев операций, которые можно повторить.

### <a name="293"></a><a name="2.9.3"/>2.9.3

* Обновление для пакета SDK для Cosmos версии 2.4.7.

### <a name="292"></a><a name="2.9.2"/>2.9.2

* Исправлена работа функции mergeAll, чтобы продолжить выполнение операции на основе идентификатора и значения ключа раздела, чтобы все обновляемые свойства документа, которые расположены после id и значения ключа раздела, добавлялись в список обновленных элементов.

### <a name="291"></a><a name="2.9.1"/>2.9.1

* Обновлена начальная степень параллелизма до значения 1 и добавлены журналы отладки для мини-пакета.


