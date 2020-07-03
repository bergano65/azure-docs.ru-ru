---
title: 'Azure Cosmos DB: API Java для пакетного исполнителя, ресурсы & SDK'
description: Сведения о пакете SDK и API Java массового исполнителя, включая даты выхода и прекращения использования, а также изменения, внесенные в каждую версию пакета SDK Java массового исполнителя в Azure Cosmos DB.
author: milismsft
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/21/2018
ms.author: adrianmi
ms.openlocfilehash: 0030f974a36dc80dc8c4112000aa5934126a2482
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836450"
---
# <a name="java-bulk-executor-library-download-information"></a>Библиотека массового исполнителя Java — информация о скачивании

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Веб-канал изменений .NET](sql-api-sdk-dotnet-changefeed.md)
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
|**Описание**|Библиотека массового исполнителя позволяет клиентским приложениям выполнять массовые операции в учетных записях Azure Cosmos DB. Библиотека массового исполнителя предоставляет пространства имен BulkImport и BulkUpdate. Модуль BulkImport может оптимизировать массовый прием документов, обеспечивая использование максимального объема пропускной способности, подготовленной для коллекции. Модуль Булкупдате может выполнять массовые обновления существующих данных в контейнерах Cosmos для Azure в качестве исправлений.|
|**Загрузка пакета SDK**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Библиотека с массовым исполнителем в GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Документация по API**| [Справочная документация по API Java](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Начало работы**|[Начало работы с пакетом SDK для Java для библиотеки массового исполнителя](bulk-executor-java.md)|
|**Минимальная поддерживаемая среда выполнения**|[Пакет Java Development Kit (JDK) 7 +](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Заметки о выпуске

### <a name="2100"></a><a name="2.10.0"/>2.10.0

* Исправление для Документанализер. Java, чтобы правильно извлечь значения ключа вложенной секции из JSON.

### <a name="294"></a><a name="2.9.4"/>2.9.4

* Добавление функциональных возможностей в операции Булкделете для повторного выполнения конкретных сбоев, а также получение списка сбоев для пользователя, который можно повторить.

### <a name="293"></a><a name="2.9.3"/>2.9.3

* Обновление для пакета SDK для Cosmos версии 2.4.7.

### <a name="292"></a><a name="2.9.2"/>2.9.2

* Исправьте значение "Мержеалл", чтобы продолжить работу с идентификатором и значением ключа секции, чтобы все обновляемые свойства документа, которые помещаются после "ID" и значения ключа секции, добавлялись в список обновленных элементов.

### <a name="291"></a><a name="2.9.1"/>2.9.1

* Измените начальную степень параллелизма на 1 и добавьте журналы отладки для уменьшив.


