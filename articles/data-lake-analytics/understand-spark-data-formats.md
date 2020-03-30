---
title: Понять форматы данных Apache Spark для разработчиков Azure Data Lake.
description: В этой статье описаны концепции Apache Spark, которые помогут разработчикам U_SQL понять различия между форматами данных U-S'L и Spark.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648445"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Понимание различий между форматами данных U-S'S и Spark

Если вы хотите использовать либо [Azure Databricks,](../azure-databricks/what-is-azure-databricks.md) либо [Azure HDInsight Spark,](../hdinsight/spark/apache-spark-overview.md)мы рекомендуем перенести данные из [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) в [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md)

В дополнение к перемещению файлов, вы также хотите, чтобы ваши данные, хранящиеся в таблицах U-S'L, доступны для Spark.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Перемещение данных, хранящихся в файлах Хранения озера Лазурных Данных Gen1

Данные, хранящиеся в файлах, могут перемещаться различными способами:

- Напишите конвейер [Azure Data Factory](../data-factory/introduction.md) для копирования данных из учетной записи [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) в учетную запись [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md)
- Напишите задание Spark, которое считывает данные из учетной записи [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) и записывает их в учетную запись [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md) На основании случая использования вы можете написать его в другом формате, например, Паркет, если вам не нужно сохранять исходный формат файла.

Рекомендуем ознакомиться со статьей [Upgrade your big data Analytics решения от Azure Data Lake Storage Gen1 до Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)

## <a name="move-data-stored-in-u-sql-tables"></a>Перемещение данных, хранящихся в таблицах U-S'L

Таблицы U-S'L не понятны Spark. Если данные хранятся в таблицах U-S'L, вы запустите задание U-S'L, которое извлекает данные таблицы и сохраняет их в формате, который понимает Spark. Наиболее подходящим форматом является создание набора файлов Паркета после макета папки Hive metastore.

Выход может быть достигнут в U-S'L со встроенным выходом паркета и с помощью динамического вывода раздела с наборами файлов для создания папок раздела. [Обработка больше файлов, чем когда-либо и использовать Паркет](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) является примером того, как создать такие Spark расходных данных.

После этого преобразования вы копируете данные, изложенные в главе [Move, данные, хранящиеся в файлах Azure Data Lake Storage Gen1.](#move-data-stored-in-azure-data-lake-storage-gen1-files)

## <a name="caveats"></a>Предупреждения

- Семантика данных при копировании файлов, копия будет происходить на уровне байт. Таким образом, те же данные должны появляться в учетной записи [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md) Обратите внимание, однако, Spark может интерпретировать некоторые символы по-разному. Например, он может использовать другой по умолчанию для разграничения строкви в файле CSV.
    Кроме того, если вы копируете набранные данные (из таблиц), то Паркет и Искра могут иметь различную точность и масштаб для некоторых набранных значений (например, поплавок) и могут по-разному относиться к нулевым значениям. Например, у U-S'SL есть семантика СЗ для нулевых значений, в то время как Spark имеет трехценную логику для нулевых значений.

- Организации данных (раздельные) таблицы U-S'L обеспечивают двухуровневое раздел. Внешний уровень ()`PARTITIONED BY`по стоимости и карты в основном в схеме раздела Hive/Spark с использованием иерархий папок. Необходимо убедиться, что значения null отображаются в правой папке. Внутренний уровень ()`DISTRIBUTED BY`в U-S'L предлагает 4 схемы распределения: круглый малиновка, диапазон, хэш и прямой хэш.
    Таблицы Hive/Spark поддерживают только раздел значения или разделх хэша, используя другую функцию хэша, чем U-S'SL. При выходе данных таблицы U-S'L вы, вероятно, сможете отобразить в себе часть значений для Spark и, возможно, потребуется провести дальнейшую настройку макета данных в зависимости от ваших окончательных запросов Spark.

## <a name="next-steps"></a>Дальнейшие действия

- [Понять концепции кода Spark для разработчиков U-S'SL](understand-spark-code-concepts.md)
- [Обновление решений для аналитики больших данных с Azure Data Lake Storage 1-го поколения до Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET для Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Преобразование данных с помощью действия Spark в фабрике данных Azure](../data-factory/transform-data-using-spark.md)
- [Преобразование данных с помощью действия Hadoop Hive в фабрике данных Azure](../data-factory/transform-data-using-hadoop-hive.md)
- [Apache Spark в Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
