---
title: Использование внешних хранилищ метаданных в Azure HDInsight
description: Используйте внешние хранилища метаданных с кластерами Azure HDInsight и рекомендациями.
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

HDInsight позволяет управлять данными и метаданными, развертывая ключевые решения метаданных и базы данных управления во внешних хранилищах данных. Эта функция в настоящее время доступна для базы данных [Apache Hive хранилище метаданных](#custom-metastore), [Apache Oozie хранилище метаданных](#apache-oozie-metastore) и [Apache Ambari](#custom-ambari-db).

Хранилище метаданных Apache Hive в HDInsight является важной частью архитектуры Apache Hadoop. Хранилище метаданных — это центральный репозиторий схемы, который могут использовать другие инструменты для доступа к большим данным, такие как Apache Spark, Interactive Query (LLAP), Presto или Apache Pig. HDInsight использует базу данных SQL Azure в качестве хранилища метаданных Hive.

![Архитектура хранилища метаданных Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Есть два способа настроить хранилище метаданных для кластеров HDInsight:

* [Хранилище метаданных по умолчанию](#default-metastore).
* [Пользовательское хранилище метаданных](#custom-metastore).

## <a name="default-metastore"></a>Хранилище метаданных по умолчанию

По умолчанию HDInsight создает хранилище метаданных с каждым типом кластера. Вместо этого можно указать пользовательское хранилище метаданных. При использовании хранилища метаданных по умолчанию учитывайте следующее:

* Дополнительные затраты не требуются. HDInsight создает хранилище метаданных с каждым типом кластера без дополнительных затрат с вашей стороны.

* Каждое хранилище метаданных по умолчанию является частью жизненного цикла кластера. При удалении кластера соответствующее хранилище метаданных и метаданные также удаляются.

* Вы не можете совместно использовать хранилище метаданных по умолчанию с другими кластерами.

* Хранилище метаданных по умолчанию использует стандартную базу данных SQL Azure с ограничением в пять DTU (единица транзакций базы данных).
Это хранилище метаданных по умолчанию обычно используется для относительно простых рабочих нагрузок, которым не требуется несколько кластеров, а метаданные не нужно сохранять за пределами жизненного цикла кластера.

## <a name="custom-metastore"></a>Пользовательское хранилище метаданных

HDInsight также поддерживает пользовательские хранилища метаданных, которые рекомендуется использовать для кластеров в рабочей среде:

* В качестве хранилища метаданных указывается собственная база данных SQL Azure.

* Жизненный цикл хранилище метаданных не привязан к жизненному циклу кластеров, поэтому вы можете создавать и удалять кластеры без потери метаданных. Метаданные, такие как схемы Hive, сохраняются даже после удаления и повторного создания кластера HDInsight.

* Пользовательское хранилище метаданных позволяет присоединять к этому хранилищу метаданных кластеры различных типов. Например, одно хранилище метаданных может совместно использоваться в кластерах Interactive Query, Hive и Hive в HDInsight.

* Вы оплачиваете стоимость хранилища метаданных (База данных SQL Azure) в соответствии с выбранным уровнем производительности.

* При необходимости хранилище метаданных можно масштабировать.

![Вариант использования хранилища метаданных Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Создание и Настройка базы данных SQL Azure для настраиваемого хранилище метаданных

Перед настройкой настраиваемого хранилище метаданных Hive для кластера HDInsight необходимо создать или использовать существующую базу данных SQL Azure.  Дополнительные сведения см. [в разделе Краткое руководство. Создание отдельной базы данных в базе данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

Чтобы обеспечить доступ кластера HDInsight к подключенной базе данных SQL Azure, настройте правила брандмауэра базы данных SQL Azure, чтобы предоставить службам и ресурсам Azure доступ к серверу.

Вы можете включить этот параметр в портал Azure, щелкнув **задать брандмауэр сервера**, а **затем выбрав пункт** **Разрешить службам и ресурсам Azure доступ к серверу** для сервера базы данных SQL Azure или базы данных. Дополнительные сведения см. в разделе [Создание правил брандмауэра IP-адресов и управление ими](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) .

![Кнопка установки брандмауэра сервера](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![разрешить доступ к службам Azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Выбор пользовательского хранилища метаданных во время создания кластера

При создании кластеру можно указать ранее созданную базу данных Azure SQL или настроить базу данных SQL после его создания. Этот параметр указывается с помощью **параметров Storage > хранилище метаданных** при создании нового кластера Hadoop, Spark или Interactive Hive из портал Azure.

![Хранилище метаданных Hive HDInsight на портале Azure](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-best-practices"></a>Рекомендации по хранилищу метаданных Hive

Ниже приведены некоторые распространенные рекомендации по хранилищу метаданных Hive HDInsight:

* По возможности используйте пользовательское хранилище метаданных, так как это поможет разделить вычислительные ресурсы в выполняемом кластере и метаданные из хранилища метаданных.

* Начните с уровня S2, предоставляющего 50 DTU и хранилище размером 250 ГБ. Если вы видите узкое место, можно увеличить масштаб базы данных.

* Если требуется несколько кластеров HDInsight для доступа к отдельным данным, используйте отдельную базу данных для хранилища метаданных для каждого кластера. Если хранилище метаданных используется совместно в нескольких кластерах HDInsight, это означает, что кластеры используют одинаковые метаданные и базовые файлы данных пользователей.

* Периодически архивируйте пользовательское хранилище метаданных. База данных SQL Azure автоматически создает резервные копии, но период их хранения различается. Дополнительные сведения см. в статье [Подробнее об автоматическом резервном копировании базы данных SQL](../sql-database/sql-database-automated-backups.md).

* Найдите свое хранилище метаданных и кластер HDInsight в одном регионе для максимальной производительности и минимальной платы за исходящий трафик сети.

* Отслеживайте производительность и доступность хранилище метаданных с помощью средств мониторинга базы данных SQL Azure, таких как журналы портал Azure или Azure Monitor.

* Если создается новая версия Azure HDInsight для существующей пользовательской базы данных хранилища метаданных, система обновляет схему хранилища метаданных, и это необратимая операция без восстановления базы данных из резервной копии.

* Если вы совместно используете хранилище метаданных в нескольких кластерах, убедитесь, что все кластеры имеют одну и ту же версию HDInsight. Различные версии Hive используют разные версии базы данных хранилища метаданных. Например, нельзя совместно использовать хранилище метаданных в кластерах Hive 2,1 и Hive 3,1 с версиями.

* В HDInsight 4,0 Spark и Hive используют независимые каталоги для доступа к таблицам SparkSQL или Hive. Таблица, созданная Spark, находится в каталоге Spark. Таблица, созданная Hive, находится в каталоге Hive. Это отличается от HDInsight 3,6, где размещается общий каталог Hive и Spark. Интеграция Hive и Spark в HDInsight 4,0 основана на соединителе хранилища Hive (HWC). HWC работает как мост между Spark и Hive. [Сведения о соединителе хранилища Hive](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Хранилище метаданных Apache Oozie

Apache Oozie — это система координации рабочих процессов, которая управляет заданиями Hadoop.  Эта система поддерживает задания Hadoop для Apache MapReduce, Pig, Hive и т. д.  Oozie использует хранилище метаданных, чтобы сохранять сведения о текущих и завершенных рабочих процессах. Для повышения производительности Oozie можно использовать Базу данных SQL Azure в качестве хранилища метаданных. Хранилище метаданных также позволяет осуществлять доступ к данным задания Oozie после удаления кластера.

Дополнительные инструкции по созданию хранилища метаданных Oozie с базой данных SQL Azure см. в статье [Использование Oozie с Hadoop для определения и запуска рабочих процессов в Azure HDInsight под управлением Linux](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Пользовательская база данных Ambari DB

Сведения о том, как использовать собственную внешнюю базу данных с Apache Ambari в HDInsight, см. в статье [пользовательские базы данных Apache Ambari](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Дополнительная информация

* [Установка кластеров в HDInsight с использованием Apache Hadoop, Apache Spark, Apache Kafka и других технологий](./hdinsight-hadoop-provision-linux-clusters.md)
