---
title: Поймите Apache Spark для разработчиков Azure Data Lake Analytics U-S'SL.
description: В этой статье описаны концепции Apache Spark, которые помогут вам отличить разработчиков U-S'SL.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 594e1055c4c063e4e151fefa3e183e6e799c90b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648432"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Основные сведения об Apache Spark для разработчиков U-SQL

Корпорация Майкрософт поддерживает несколько сервисов аналитики, таких как [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) и [Azure HDInsight,](../hdinsight/hdinsight-overview.md) а также Аналитику озер данных Azure. Мы слышим от разработчиков, что они имеют четкое предпочтение с открытым исходным кодом решений, как они строят аналитические трубопроводы. Чтобы помочь разработчикам U-S'SL понять Apache Spark и как можно преобразовать скрипты U-S'L в Apache Spark, мы создали это руководство.

Она включает в себя ряд шагов, которые вы можете предпринять, и несколько альтернатив.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Шаги по преобразованию U-S'L в Apache Spark

1. Преобразуйте конвейеры оркестровки вакансий.

   Если вы используете [фабрику данных Azure](../data-factory/introduction.md) для организации скриптов Analytics Данных Azure, вам придется настроить их для организации новых программ Spark.
2. Поймите за разницу между тем, как U-S'L и Spark управляют данными

   Если вы хотите перенести данные из [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) в [Azure Data Lake Storage Gen2,](../storage/blobs/data-lake-storage-introduction.md)вам придется скопировать как данные файла, так и сохраненные данные каталога. Обратите внимание, что Аналитика озер данных Azure поддерживает только систему хранения данных Azure Data Lake Storage Gen1. Посмотреть [форматы данных «Понять искры»](understand-spark-data-formats.md)
3. Преобразование скриптов U-S'L в Spark

   Перед преобразованием скриптов U-S'L вам придется выбрать аналитический сервис. Некоторые из доступных вычислительных услуг:
      - [Процесс данных фабрики данных AzureFlow](../data-factory/concepts-data-flow-overview.md) Потоки картданных данных представляют собой визуально разработанные преобразования данных, которые позволяют инженерам по обработке графических данных развивать логику преобразования графических данных без написания кода. Хотя они и не подходят для выполнения сложного пользовательского кода, они могут легко представлять традиционные преобразования потока данных, подобные S'L
      - [Лазурный HDInsight Улей](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) Apache Hive на HDInsight подходит для операций по извлечению, преобразованию и загрузке (ETL). Это означает, что вы собираетесь перевести скрипты U-S'L на Apache Hive.
      - Apache Spark Engines, такие как [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) или [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) Это означает, что вы собираетесь перевести скрипты U-S'L в Spark. Для получения дополнительной [информации см.](understand-spark-data-formats.md)

> [!CAUTION]
> Как [Azure Databricks,](../azure-databricks/what-is-azure-databricks.md) так и [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) — это кластерные службы, а не бессерверные задания, такие как Аналитика Azure Data Lake. Вам придется рассмотреть вопрос о том, как обеспечить кластеры, чтобы получить соответствующее соотношение затрат/производительности и как управлять их жизни, чтобы свести к минимуму ваши расходы. Эти службы имеют различные характеристики производительности с кодом пользователя, написанным в .NET, так что вам придется либо написать обертки или переписать свой код на поддерживаемом языке. Для получения дополнительной информации [см. Форматы данных Understand Spark,](understand-spark-data-formats.md) [понимание концепций кода Apache Spark для разработчиков U-S'L,](understand-spark-code-concepts.md) [.Net для Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Дальнейшие действия

- [Понимание форматов данных Spark для разработчиков U-S'SL](understand-spark-data-formats.md)
- [Понять концепции кода Spark для разработчиков U-S'SL](understand-spark-code-concepts.md)
- [Обновление решений для аналитики больших данных с Azure Data Lake Storage 1-го поколения до Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET для Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Преобразование данных с помощью действия Hadoop Hive в фабрике данных Azure](../data-factory/transform-data-using-hadoop-hive.md)
- [Преобразование данных с помощью действия Spark в фабрике данных Azure](../data-factory/transform-data-using-spark.md)
- [Apache Spark в Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
