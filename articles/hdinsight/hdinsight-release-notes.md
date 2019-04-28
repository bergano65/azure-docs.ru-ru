---
title: Заметки о выпуске Azure HDInsight
description: Последние заметки о выпуске Azure HDInsight. Получите советы по разработке и сведения для Hadoop, Spark, R Server, Hive и многое другое.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 0beac64ceb0bbf729d2f6d0f6a0ca8d5af499f42
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760115"
---
# <a name="release-notes-for-azure-hdinsight"></a>Заметки о выпуске Azure HDInsight

Эта статья содержит сведения о **последних** обновлениях выпуска Azure HDInsight. Дополнительные сведения о предыдущих выпусках см. в статье [Заметки о выпуске для компонентов Hadoop в Azure HDInsight (архив)](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в [статье об управлении версиями HDInsight](hdinsight-component-versioning.md).

## <a name="summary"></a>Сводка

Azure HDInsight является одной из самых популярных служб среди корпоративных клиентов для аналитики с открытым кодом Apache Hadoop и Apache Spark в Azure.

## <a name="new-features"></a>новые функции;

Дополнительные сведения о важных изменениях, с помощью HDInsight 4.0., см. в разделе [новые возможности в HDI 4.0?](../hdinsight/hdinsight-version-release.md).

## <a name="component-versions"></a>Версии компонентов

Ниже приведен официальной версии Apache все компоненты HDInsight 4.0. Компоненты, перечисленные являются версиях указанные самой последней стабильной версии.

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1
- Apache Mahout 0.9.0+
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.1
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

Более поздние версии компонентов Apache иногда объединяются в распространении HDP, помимо версий, перечисленных выше. В этом случае эти более поздние версии приводятся в таблице технических предварительных версий и не должны заменять версии компонента Apache указанного выше списка в рабочей среде.

## <a name="apache-patch-information"></a>Сведения об исправлениях Apache

Дополнительные сведения об исправлениях, доступные в HDInsight 4.0 см. в разделе исправление для каждого продукта в таблице ниже.

| Название продукта | Сведения об исправлении |
|---|---|
| Ambari | [Сведения об исправлении Ambari](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Сведения об исправлении Hadoop](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| hbase | [Сведения об исправлении HBase](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Этот выпуск предоставляет 3.1.0 с дополнительных исправлений, Apache Hive.  |
| Kafka | Этот выпуск предоставляет 1.1.1 с без дополнительных обновлений Apache Kafka. |
| Oozie, | [Сведения об исправлении Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Сведения об исправлении Phoenix](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig, | [Сведения об исправлении pig](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Сведения об исправлении ranger](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Сведения об исправлении Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Этот выпуск предоставляет 1.4.7 с без дополнительных обновлений Apache Sqoop. |
| Tez | Этот выпуск предоставляет 0.9.1 с без дополнительных обновлений Apache Tez. |
| Zeppelin | Этот выпуск предоставляет 0.8.0 с без дополнительных обновлений Apache Zeppelin. |
| Zookeeper | [Сведения об исправлении zookeeper](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Исправлены проблемы, связанные с распространенными уязвимостями и рисками

Дополнительные сведения о безопасности проблем, устраненных в этом выпуске, см. в разделе Hortonworks [предопределенной присвоен для HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Известные проблемы

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>Репликация не работает для защиты HBase с помощью установки по умолчанию

Для HDInsight версии 4.0 выполните следующие действия:

1. Включите обмен данными между кластерами.
1. Войдите в активном головном узле.
1. Скачайте сценарий для включения репликации, выполнив следующую команду:

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Введите команду `sudo kinit <domainuser>`.
1. Введите следующую команду для запуска сценария:

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
Для HDInsight 3.6 выполните следующие действия.

1. Войдите в active HMaster ZK.
1. Скачайте сценарий для включения репликации, выполнив следующую команду:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Введите команду `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`.
1. Введите следующую команду:

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Phoenix Sqlline перестает работать после миграции кластера HBase HDInsight 4.0

Сделайте следующее:

1. Удалите следующие таблицы Phoenix:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Если не удается удалить любой таблицы, перезапустите HBase, чтобы очистить все подключения к таблицам.
1. Еще раз запустите `sqlline.py`. Phoenix повторно создают все таблицы, которые были удалены на шаге 1.
1. Повторное создание таблицы Phoenix и представления данных HBase.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>Phoenix Sqlline перестает работать после репликации метаданных HBase Phoenix с HDInsight 3.6 в 4.0

Сделайте следующее:

1. Прежде чем выполнять репликацию, перейдите к кластеру назначения 4.0 и запустите программу `sqlline.py`. Эта команда создаст таблицы Phoenix, как `SYSTEM.MUTEX` и `SYSTEM.LOG` , существуют только в версии 4.0.
1. Удалите следующие таблицы:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. Запустить репликацию HBase

## <a name="deprecation"></a>Устаревшее

Apache Storm и машинного Обучения службы недоступны в HDInsight 4.0.
