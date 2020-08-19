---
title: Заметки о выпуске и ресурсы для SQL API Azure Cosmos DB Apache Spark
description: Узнайте все о соединителе Azure Cosmos DB Apache Spark для API SQL, включая даты выпуска, даты выбытия и изменения, внесенные в каждую версию пакета SDK для SQL Async Java Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 46ddbd18051ffa44232468704ce189d4171b50e7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590015"
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

Ускорьте анализ больших данных с помощью соединителя Azure Cosmos DB Apache Spark для Core (SQL). Соединитель Spark позволяет выполнять задания [Spark ](https://spark.apache.org/) для данных, хранящихся в Azure Cosmos DB. Поддерживаются Пакетная и Потоковая обработка.

Вы можете использовать соединитель с [Azure Databricks](https://azure.microsoft.com/services/databricks) или [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), который предоставляет управляемые кластеры Spark в Azure. В следующей таблице показаны поддерживаемые версии Spark.

| Компонент | Версия |
|---------|-------|
| Apache Spark | 2.4. x, 2.3. x, 2.2. x и 2.1. x |
| Scala | 2.11 |
| Версия среды выполнения Azure Databricks | > 3.4 |

> [!WARNING]
> Этот соединитель поддерживает API ядра (SQL) Azure Cosmos DB.
> Для Cosmos DB для API-интерфейса MongoDB используйте [соединитель MongoDB Spark](https://docs.mongodb.com/spark-connector/master/).
> Для Cosmos DB API Cassandra используйте [соединитель Spark Cassandra](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="helpful-content"></a>Полезное содержимое

| Содержимое | Ссылка |
|---|---|
| **Скачивание пакета SDK** | [Скачать с Apache Spark](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) |
|**Документация по API** | [Справочник по соединителю Spark]() |
|**Участие в разработке пакета SDK** | [Соединитель Azure Cosmos DB для Apache Spark на GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Начало работы** | [Ускорение аналитики больших данных с помощью соединителя Apache Spark для Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [Использование структурированной потоковой передачи Apache Spark с Apache Kafka в Azure Cosmos DB](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>История выпусков

### <a name="311"></a>3.1.1
#### <a name="new-features"></a>новые функции;
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправляет случайный пограничные контрольные точки потоковой передачи, где в поле "ID" содержится символ "|" с примененной конфигурацией "Чанжефидмакспажеспербатч"

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>новые функции;
* Добавляет поддержку для выполнения массовых обновлений при использовании вложенных ключей разделов
* Добавляет поддержку типов данных Decimal и float во время операций записи в Cosmos DB.
* Добавляет поддержку типов меток времени, если в качестве значения используется Long (эпоха UNIX).
#### <a name="key-bug-fixes"></a>Исправления основных ошибок

### <a name="308"></a>3.0.8
#### <a name="new-features"></a>новые функции;
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Устраняет исключение приведения типов при использовании конфигурации "Вритесраугхпутбуджет".

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>новые функции;
* Добавляет сведения об ошибке для массовых сбоев в исключения и журнал.
#### <a name="key-bug-fixes"></a>Исправления основных ошибок

### <a name="306"></a>3.0.6
#### <a name="new-features"></a>новые функции;
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Устраняет проблемы с контрольными точками потоковой передачи.

### <a name="305"></a>3.0.5
#### <a name="new-features"></a>новые функции;
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Устраняет непреднамеренное исправление уровня ведения журнала сообщения об ОШИБКе уровня, чтобы уменьшить шум

### <a name="304"></a>3.0.4
#### <a name="new-features"></a>новые функции;
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Устраняет ошибку в структурированной потоковой передаче во время разбиения секций. возможно, в результате отсутствуют некоторые записи веб-канала изменений или отображаются исключения NULL для операций записи контрольной точки.

### <a name="303"></a>3.0.3
#### <a name="new-features"></a>новые функции;
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправление ошибки, при которой пользовательская схема, предоставленная для Реадстреам, игнорируется.

### <a name="302"></a>3.0.2
#### <a name="new-features"></a>новые функции;
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправления регрессии (незатененный JAR-файл включает все затененные зависимости), что увеличивает время сборки на 50%

### <a name="301"></a>3.0.1
#### <a name="new-features"></a>новые функции;
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Устраняет проблему зависимости, которая вызывает сбой прямого транспорта по протоколу TCP для Рекуесттимеаутексцептион

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>новые функции;
* Улучшение управления подключениями и пулов соединений для сокращения числа вызовов метаданных
#### <a name="key-bug-fixes"></a>Исправления основных ошибок

## <a name="faq"></a>ВОПРОСЫ И ОТВЕТЫ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Cosmos DB см. на странице службы [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Дополнительные сведения о Apache Spark см. [на домашней странице](https://spark.apache.org/).