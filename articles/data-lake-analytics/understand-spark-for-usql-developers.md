---
title: Общие сведения о Apache Spark для разработчиков Azure Data Lake Analytics U-SQL.
description: В этой статье описываются Apache Spark концепции, которые могут помочь в различиях между разработчиками U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 567574e65fcc1db3ef9e8aea73c6a59be0594f72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132302"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Основные сведения об Apache Spark для разработчиков U-SQL

Корпорация Майкрософт поддерживает несколько служб аналитики, таких как [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) и [Azure HDInsight](../hdinsight/hdinsight-overview.md) , а также Azure Data Lake Analytics. Мы будем слышать разработчиков о том, что они имеют четкие настройки для решений с открытым исходным кодом, так как они создают конвейеры аналитики. Чтобы помочь разработчикам U-SQL понять, Apache Spark и как можно преобразовать скрипты U-SQL в Apache Spark, мы создали это руководство.

Он включает ряд действий, которые можно выполнить, и несколько альтернатив.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Действия по преобразованию U-SQL в Apache Spark

1. Преобразуйте конвейеры оркестрации заданий.

   Если вы используете [фабрику данных Azure](../data-factory/introduction.md) для управления сценариями Azure Data Lake Analytics, вам придется настроить их для управления новыми программами Spark.
2. Сведения о различиях между управлением данными в U-SQL и Spark

   Если вы хотите переместить данные из [Azure Data Lake Storage 1-го поколения](../data-lake-store/data-lake-store-overview.md) в [Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-introduction.md), необходимо скопировать данные файлов и данные, обслуживаемые каталогом. Обратите внимание, что Azure Data Lake Analytics поддерживает только Azure Data Lake Storage 1-го поколения. См. раздел [Общие сведения о форматах данных Spark](understand-spark-data-formats.md)
3. Преобразование скриптов U-SQL в Spark

   Перед преобразованиями скриптов U-SQL необходимо выбрать службу аналитики. Ниже перечислены некоторые доступные службы вычислений.
      - [Поток данных фабрики Azure](../data-factory/concepts-data-flow-overview.md) Сопоставление потоков данных — это визуально разработанные преобразования данных, позволяющие инженерам по разработке данных разрабатывать логику преобразования графических данных без написания кода. Хотя это и не подходит для выполнения сложного пользовательского кода, они могут легко представлять традиционные преобразования потоков данных, схожие с SQL.
      - [Куст Azure HDInsight](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) Apache Hive в HDInsight подходит для операций извлечения, преобразования и загрузки (ETL). Это означает, что вы собираетесь преобразовывать скрипты U-SQL в Apache Hive.
      - Apache Spark такие механизмы, как [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) или [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) это означает, что вы собираетесь переводить скрипты U-SQL в Spark. Дополнительные сведения см. в статье сведения о [форматах данных Spark](understand-spark-data-formats.md) .

> [!CAUTION]
> И [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) , и [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) являются службами кластера, а не бессерверными заданиями, такими как Azure Data Lake Analytics. Вам придется подумать о том, как подготавливать кластеры, чтобы получить соответствующее соотношение затрат и производительности, а также как управлять временем существования, чтобы снизить затраты. Эти службы имеют различные характеристики производительности с кодом пользователя, написанным на платформе .NET, поэтому необходимо либо написать оболочки, либо переписать код на поддерживаемом языке. Дополнительные сведения см. в статьях [Знакомство с форматами данных Spark](understand-spark-data-formats.md), сведения [о Apache Spark концепциях кода для разработчиков U-SQL](understand-spark-code-concepts.md), [.NET для Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о форматах данных Spark для разработчиков U-SQL](understand-spark-data-formats.md)
- [Основные сведения о концепциях кода Spark для разработчиков U-SQL](understand-spark-code-concepts.md)
- [Обновление решений для аналитики больших данных с Azure Data Lake Storage 1-го поколения до Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET для Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Преобразование данных с помощью действия Hadoop Hive в фабрике данных Azure](../data-factory/transform-data-using-hadoop-hive.md)
- [Преобразование данных с помощью действия Spark в фабрике данных Azure](../data-factory/transform-data-using-spark.md)
- [Apache Spark в Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
