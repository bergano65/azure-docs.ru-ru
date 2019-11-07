---
title: Сведения о форматах данных Apache Spark для разработчиков Azure Data Lake Analytics U-SQL.
description: В этой статье описываются Apache Spark концепции, которые помогут U_SQL разработчикам понять различия между форматами данных U-SQL и Spark.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648445"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Общие сведения о различиях между форматами данных U-SQL и Spark

Если вы хотите использовать либо [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) , либо [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md), рекомендуется перенести данные из [Azure Data Lake Storage 1-го поколения](../data-lake-store/data-lake-store-overview.md) в [Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-introduction.md).

Помимо перемещения файлов, необходимо также сделать данные, хранящиеся в таблицах U-SQL, доступными для Spark.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Перемещение данных, хранящихся в файлах Azure Data Lake Storage 1-го поколения

Данные, хранящиеся в файлах, можно перемещать различными способами.

- Создайте конвейер [фабрики данных Azure](../data-factory/introduction.md) , чтобы скопировать данные из учетной записи [Azure Data Lake Storage 1-го поколения](../data-lake-store/data-lake-store-overview.md) в учетную запись [Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-introduction.md) .
- Напишите задание Spark, которое считывает данные из учетной записи [Azure Data Lake Storage 1-го поколения](../data-lake-store/data-lake-store-overview.md) и записывает их в учетную запись [Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-introduction.md) . В зависимости от варианта использования может потребоваться написать его в другом формате, например Parquet, если не нужно сохранять исходный формат файла.

Рекомендуется ознакомиться со статьей [Обновление решений аналитики больших данных с Azure Data Lake Storage 1-го поколения до Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-upgrade.md)

## <a name="move-data-stored-in-u-sql-tables"></a>Перемещение данных, хранящихся в таблицах U-SQL

В Spark не понимаются таблицы U-SQL. При наличии данных, хранящихся в таблицах U-SQL, вы запускаете задание U-SQL, которое извлекает данные таблицы и сохраняет их в формате, понятном Spark. Наиболее подходящий формат — создание набора Parquet-файлов после разметки папки хранилище метаданных Hive.

Выходные данные можно получить в U-SQL со встроенным Parquetом вывода и использовать динамическое секционирование выходных данных с наборами файлов для создания папок разделов. [Обработайте больше файлов, чем когда бы то ни было, и используйте Parquet](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) , чтобы получить пример создания таких данных, которые можно использовать для Spark.

После этого преобразования данные копируются, как описано в главе [Перемещение данных, хранящихся в файлах Azure Data Lake Storage 1-го поколения](#move-data-stored-in-azure-data-lake-storage-gen1-files).

## <a name="caveats"></a>Предупреждения

- Семантика данных при копировании файлов копия будет выполняться на уровне Byte. Поэтому одни и те же данные должны отображаться в учетной записи [Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-introduction.md) . Обратите внимание, однако Spark может интерпретировать некоторые символы по-разному. Например, он может использовать другое значение по умолчанию для разделителя строк в CSV-файле.
    Более того, если вы копируете типизированные данные (из таблиц), то Parquet и Spark могут иметь разную точность и масштаб для некоторых из типизированных значений (например, с плавающей запятой) и могут обрабатывать значения NULL по-разному. Например, U-SQL имеет C# семантику для значений NULL, а Spark имеет 3-значную логику для значений NULL.

- Организация данных (секционирование). таблицы U-SQL предоставляют два уровня секционирования. Внешний уровень (`PARTITIONED BY`) является значением и сопоставляет преимущественно с схемой секционирования Hive/Spark с помощью иерархий папок. Необходимо убедиться, что значения NULL сопоставлены с правой папкой. Внутренний уровень (`DISTRIBUTED BY`) в U-SQL предлагает 4 схемы распределения: циклический перебор, диапазон, хэш и прямой хэш.
    Таблицы Hive/Spark поддерживают секционирование значений или хэш-секционирование, используя другую хэш-функцию, отличную от U-SQL. При выводе данных из таблицы U-SQL вы, вероятно, сможете сопоставляться только с секционированием значений для Spark и может потребоваться дополнительная настройка макета данных в зависимости от ваших заключительных запросов Spark.

## <a name="next-steps"></a>Дальнейшие действия

- [Основные сведения о концепциях кода Spark для разработчиков U-SQL](understand-spark-code-concepts.md)
- [Обновление решений аналитики больших данных с Azure Data Lake Storage 1-го поколения до Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET для Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Преобразование данных с помощью действия Spark в фабрике данных Azure](../data-factory/transform-data-using-spark.md)
- [Преобразование данных с помощью действия Hadoop Hive в фабрике данных Azure](../data-factory/transform-data-using-hadoop-hive.md)
- [Что такое Apache Spark в Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
