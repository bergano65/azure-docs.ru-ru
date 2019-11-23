---
title: Использование внешних хранилищ метаданных в Azure HDInsight
description: Use external metadata stores with Azure HDInsight clusters, and best practices.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 14b36a391778649e96694f1cb1d3a1b4e7ee89ba
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327370"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Использование внешних хранилищ метаданных в Azure HDInsight

HDInsight allows you to take control of your data and metadata by deploying key metadata solutions and management databases to external data stores. This feature is currently available for [Apache Hive metastore](#custom-metastore), [Apache Oozie metastore](#apache-oozie-metastore) and [Apache Ambari database](#custom-ambari-db).

Хранилище метаданных Apache Hive в HDInsight является важной частью архитектуры Apache Hadoop. Хранилище метаданных — это центральный репозиторий схемы, который могут использовать другие инструменты для доступа к большим данным, такие как Apache Spark, Interactive Query (LLAP), Presto или Apache Pig. HDInsight использует базу данных SQL Azure в качестве хранилища метаданных Hive.

![Архитектура хранилища метаданных Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Есть два способа настроить хранилище метаданных для кластеров HDInsight:

* [Хранилище метаданных по умолчанию](#default-metastore).
* [Пользовательское хранилище метаданных](#custom-metastore).

## <a name="default-metastore"></a>Хранилище метаданных по умолчанию

По умолчанию HDInsight создает хранилище метаданных с каждым типом кластера. Вместо этого можно указать пользовательское хранилище метаданных. При использовании хранилища метаданных по умолчанию учитывайте следующее:

* Дополнительные затраты не требуются. HDInsight создает хранилище метаданных с каждым типом кластера без дополнительных затрат с вашей стороны.

* Каждое хранилище метаданных по умолчанию является частью жизненного цикла кластера. При удалении кластера соответствующее хранилище метаданных и метаданные также удаляются.

* You can't share the default metastore with other clusters.

* Хранилище метаданных по умолчанию использует стандартную базу данных SQL Azure с ограничением в пять DTU (единица транзакций базы данных).
Это хранилище метаданных по умолчанию обычно используется для относительно простых рабочих нагрузок, которым не требуется несколько кластеров, а метаданные не нужно сохранять за пределами жизненного цикла кластера.

## <a name="custom-metastore"></a>Пользовательское хранилище метаданных

HDInsight также поддерживает пользовательские хранилища метаданных, которые рекомендуется использовать для кластеров в рабочей среде:

* В качестве хранилища метаданных указывается собственная база данных SQL Azure.

* The lifecycle of the metastore isn't tied to a clusters lifecycle, so you can create and delete clusters without losing metadata. Метаданные, такие как схемы Hive, сохраняются даже после удаления и повторного создания кластера HDInsight.

* Пользовательское хранилище метаданных позволяет присоединять к этому хранилищу метаданных кластеры различных типов. Например, одно хранилище метаданных может совместно использоваться в кластерах Interactive Query, Hive и Hive в HDInsight.

* Вы оплачиваете стоимость хранилища метаданных (База данных SQL Azure) в соответствии с выбранным уровнем производительности.

* При необходимости хранилище метаданных можно масштабировать.

![Вариант использования хранилища метаданных Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Create and config Azure SQL Database for the custom metastore

You need to create or have an existing Azure SQL Database before setting up a custom Hive metastore for a HDInsight cluster.  For more information, see [Quickstart: Create a single database in Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

To make sure that your HDInsight cluster can access the connected Azure SQL Database, configure Azure SQL Database firewall rules to allow Azure services and resources to access the server.

You can enable this option in the Azure portal by clicking **Set server firewall**, and clicking **ON** underneath **Allow Azure services and resources to access this server** for the Azure SQL Database server or database. For more information, see [Create and manage IP firewall rules](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

![set server firewall button](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![allow azure services access](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Выбор пользовательского хранилища метаданных во время создания кластера

При создании кластеру можно указать ранее созданную базу данных Azure SQL или настроить базу данных SQL после его создания. This option is specified with the **Storage > Metastore settings** while creating a new Hadoop, Spark, or interactive Hive cluster from Azure portal.

![Хранилище метаданных Hive HDInsight на портале Azure](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-best-practices"></a>Рекомендации по хранилищу метаданных Hive

Ниже приведены некоторые распространенные рекомендации по хранилищу метаданных Hive HDInsight:

* По возможности используйте пользовательское хранилище метаданных, так как это поможет разделить вычислительные ресурсы в выполняемом кластере и метаданные из хранилища метаданных.

* Начните с уровня S2, предоставляющего 50 DTU и хранилище размером 250 ГБ. Если вы видите узкое место, можно увеличить масштаб базы данных.

* Если требуется несколько кластеров HDInsight для доступа к отдельным данным, используйте отдельную базу данных для хранилища метаданных для каждого кластера. Если хранилище метаданных используется совместно в нескольких кластерах HDInsight, это означает, что кластеры используют одинаковые метаданные и базовые файлы данных пользователей.

* Периодически архивируйте пользовательское хранилище метаданных. База данных SQL Azure автоматически создает резервные копии, но период их хранения различается. Дополнительные сведения см. в статье [Подробнее об автоматическом резервном копировании базы данных SQL](../sql-database/sql-database-automated-backups.md).

* Найдите свое хранилище метаданных и кластер HDInsight в одном регионе для максимальной производительности и минимальной платы за исходящий трафик сети.

* Monitor your metastore for performance and availability using Azure SQL Database Monitoring tools, such as the Azure portal or Azure Monitor logs.

* Если создается новая версия Azure HDInsight для существующей пользовательской базы данных хранилища метаданных, система обновляет схему хранилища метаданных, и это необратимая операция без восстановления базы данных из резервной копии.

* Если вы совместно используете хранилище метаданных в нескольких кластерах, убедитесь, что все кластеры имеют одну и ту же версию HDInsight. Различные версии Hive используют разные версии базы данных хранилища метаданных. For example, you can't share a metastore across Hive 2.1 and Hive 3.1 versioned clusters.

* In HDInsight 4.0, Spark and Hive use independent catalogs for accessing SparkSQL or Hive tables. A table created by Spark resides in the Spark catalog. A table created by Hive resides in the Hive catalog. This is different than HDInsight 3.6 where Hive and Spark shared common catalog. Hive and Spark Integration in HDInsight 4.0 relies on Hive Warehouse Connector (HWC). HWC works as a bridge between Spark and Hive. [Learn about Hive Warehouse Connector](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Apache Oozie metastore

Apache Oozie — это система координации рабочих процессов, которая управляет заданиями Hadoop.  Эта система поддерживает задания Hadoop для Apache MapReduce, Pig, Hive и т. д.  Oozie использует хранилище метаданных, чтобы сохранять сведения о текущих и завершенных рабочих процессах. Для повышения производительности Oozie можно использовать Базу данных SQL Azure в качестве хранилища метаданных. Хранилище метаданных также позволяет осуществлять доступ к данным задания Oozie после удаления кластера.

Дополнительные инструкции по созданию хранилища метаданных Oozie с базой данных SQL Azure см. в статье [Использование Oozie с Hadoop для определения и запуска рабочих процессов в Azure HDInsight под управлением Linux](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Пользовательская база данных Ambari DB

To use your own external database with Apache Ambari on HDInsight, see [Custom Apache Ambari database](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Установка кластеров в HDInsight с использованием Apache Hadoop, Apache Spark, Apache Kafka и других технологий](./hdinsight-hadoop-provision-linux-clusters.md)
