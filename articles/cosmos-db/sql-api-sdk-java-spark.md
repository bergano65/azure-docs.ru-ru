---
title: Заметки о выпуске и ресурсы для SQL API Cosmos DB Apache Spark
description: Сведения о соединителе Apache Spark Azure Cosmos DB для API SQL, включая даты выпуска, даты прекращения использования и изменения, внесенные в каждую версию пакета SDK для SQL Async Java Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 05f81e4d93244db854bf8d0ec254ee647f81d9cc
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069175"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark соединителя для API Core (SQL): заметки о выпуске и ресурсы
> [!div class="op_single_selector"]
> * [Пакет SDK версии 3 для .NET](sql-api-sdk-dotnet-standard.md)
> * [Пакет SDK для .NET версии 2](sql-api-sdk-dotnet.md)
> * [Пакет SDK для .NET Core версии 2](sql-api-sdk-dotnet-core.md)
> * [Пакет SDK для веб-канала изменений .NET версии 2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Пакет SDK для Java версии 4](sql-api-sdk-java-v4.md)
> * [Пакет SDK для Async Java версии 2](sql-api-sdk-async-java.md)
> * [Пакет SDK для Sync Java версии 2](sql-api-sdk-java.md)
> * [Пружинные данные v2](sql-api-sdk-java-spring-v2.md)
> * [Пружинные данные v3](sql-api-sdk-java-spring-v3.md)
> * [Соединитель Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Поставщик ресурсов REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Групповой исполнитель — .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Исполнитель массовых операций — Java](sql-api-sdk-bulk-executor-java.md)

Можно ускорить анализ больших данных с помощью соединителя Azure Cosmos DB Apache Spark для Core (SQL). Соединитель Spark позволяет выполнять задания [Spark](https://spark.apache.org/) для данных, хранящихся в Azure Cosmos DB. Поддерживаются Пакетная и Потоковая обработка.

Вы можете использовать соединитель с [Azure Databricks](https://azure.microsoft.com/services/databricks) или [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), который предоставляет управляемые кластеры Spark в Azure. В следующей таблице приведены поддерживаемые версии.

| Компонент | Версия |
|---------|-------|
| Apache Spark | 2,4.*x*, 2,3. *x*, 2,2. *x*и 2,1. *x* |
| Scala | 2,11 |
| Azure Databricks (версия среды выполнения) | Позже 3,4 |

> [!WARNING]
> Этот соединитель поддерживает API ядра (SQL) Azure Cosmos DB.
> Для Cosmos DB API для MongoDB используйте [соединитель MongoDB для Spark](https://docs.mongodb.com/spark-connector/master/).
> Для API Cassandra Cosmos DB используйте [соединитель Spark Cassandra](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="resources"></a>Ресурсы

| Ресурс | Ссылка |
|---|---|
| **Скачивание пакета SDK** | [Скачать с Apache Spark](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) |
|**Документация по API** | [Справочник по соединителю Spark]() |
|**Участие в пакете SDK** | [Соединитель Azure Cosmos DB для Apache Spark на GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Начало работы** | [Ускорение аналитики больших данных с помощью соединителя Apache Spark для Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [Использование структурированной потоковой передачи Apache Spark с Apache Kafka в Azure Cosmos DB](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>История выпусков

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправляет промежуточный вариант контрольной точки потоковой передачи, в котором параметр `ID` содержит символ вертикальной черты (|) с `ChangeFeedMaxPagesPerBatch` примененной конфигурацией.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Новые функции
* Добавляет поддержку для выполнения массовых обновлений при использовании вложенных ключей секционирования.
* Добавляет поддержку типов данных Decimal и float во время операций записи в Azure Cosmos DB.
* Добавляет поддержку типов меток времени, если в качестве значения используется Long (эпоха UNIX).

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправляет исключение приведения, возникающее при `WriteThroughputBudget` использовании конфигурации.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Новые функции
* Добавляет сведения об ошибке для массовых сбоев в исключения и журнал.

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Устраняет проблемы с контрольными точками потоковой передачи.

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Чтобы уменьшить уровень шума, исправляет непреднамеренное сообщение об ОШИБКе уровня журнала.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Устраняет ошибку в структурированной потоковой передаче во время разбиения секций. Ошибка может привести к некоторым отсутствующим записям веб-канала изменений или исключениям NULL для операций записи контрольной точки.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Устраняет ошибку, которая приводит к игнорированию пользовательской схемы, предоставленной для Реадстреам.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправлена регрессия (незатененный JAR-файл, включающий все затененные зависимости), увеличивающий время сборки на 50 процентов.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Устраняет проблему зависимости, которая приводит к сбою прямого транспорта по протоколу TCP с Рекуесттимеаутексцептион.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Новые функции
* Улучшает управление подключениями и объединение соединений, чтобы сократить количество вызовов метаданных.

## <a name="faq"></a>ВОПРОСЫ И ОТВЕТЫ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Дальнейшие действия

См. дополнительные сведения об [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Узнайте больше об [Apache Spark](https://spark.apache.org/).