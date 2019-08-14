---
title: Заметки о выпуске Azure HDInsight
description: Последние заметки о выпуске Azure HDInsight. Получите советы по разработке и подробные сведения о Hadoop, Spark, R Server, Hive и других.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 7c31520efd881e8e0b5ed309f62d273bac59c0e3
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945027"
---
# <a name="release-notes"></a>Заметки о выпуске

Эта статья содержит сведения о **последних** обновлениях выпуска Azure HDInsight. Дополнительные сведения о предыдущих выпусках см. в статье [Заметки о выпуске для компонентов Hadoop в Azure HDInsight (архив)](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в [статье об управлении версиями HDInsight](hdinsight-component-versioning.md).

## <a name="summary"></a>Сводка

Azure HDInsight является одной из самых популярных служб среди корпоративных клиентов для аналитики с открытым кодом Apache Hadoop и Apache Spark в Azure.

## <a name="new-features"></a>новые функции;

Дополнительные сведения о важных изменениях в HDInsight 4,0 см. в статье [новые возможности в HDI 4,0](../hdinsight/hdinsight-version-release.md).

## <a name="component-versions"></a>Версии компонентов

Официальные версии Apache всех компонентов HDInsight 4,0 приведены ниже. Перечисленные компоненты являются выпусками самых последних стабильных версий.

- 2\.7.1 Apache Ambari
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1, 2.1.0
- Apache Mahout 0.9.0+
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.1, 2.4.0
- 1\.4.7 Apache Sqoop
- 0\.9.1 Apache TEZ
- 0\.8.0 Apache Zeppelin
- Apache ZooKeeper 3.4.6

Более поздние версии компонентов Apache иногда объединяются в дистрибутив HDP в дополнение к приведенным выше версиям. В этом случае эти более поздние версии приводятся в таблице технических предварительных версий и не должны заменять версии компонента Apache указанного выше списка в рабочей среде.

## <a name="apache-patch-information"></a>Сведения об исправлениях Apache

Дополнительные сведения об исправлениях, доступных в HDInsight 4,0, см. в списке исправлений для каждого продукта в таблице ниже.

| Название продукта | Сведения об исправлении |
|---|---|
| Ambari | [Сведения об исправлении Ambari](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Сведения об исправлении Hadoop](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| Hbase | [Сведения об исправлении HBase](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Этот выпуск предоставляет Hive 3.1.0 без дополнительных исправлений Apache.  |
| Kafka | Этот выпуск предоставляет Kafka 1.1.1 без дополнительных исправлений Apache. |
| Oozie | [Сведения об исправлении Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Сведения об исправлении Phoenix](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Сведения об исправлении Pig](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Сведения об исправлении Ranger](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Сведения об исправлении Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Этот выпуск предоставляет Sqoop 1.4.7 без дополнительных исправлений Apache. |
| Tez | Этот выпуск предоставляет Tez 0.9.1 без дополнительных исправлений Apache. |
| Zeppelin | Этот выпуск предоставляет Zeppelin 0.8.0 без дополнительных исправлений Apache. |
| Zookeeper | [Сведения об исправлении Zookeeper](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Исправлены проблемы, связанные с распространенными уязвимостями и рисками

Дополнительные сведения о проблемах безопасности, устраняемых в этом выпуске, см. в разделе Hortonworks " [Устранение распространенных уязвимостей и раскрытия для HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Известные проблемы

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>Репликация не работает для защиты HBase с установкой по умолчанию

Для HDInsight 4,0 выполните следующие действия.

1. Включить обмен данными между кластерами.
1. Войдите в Active головного узла.
1. Скачайте скрипт для включения репликации с помощью следующей команды:

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Введите команду `sudo kinit <domainuser>`.
1. Введите следующую команду, чтобы выполнить скрипт:

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
Для HDInsight 3,6 выполните следующие действия.

1. Войдите в Active HMaster ZK.
1. Скачайте скрипт для включения репликации с помощью следующей команды:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Введите команду `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`.
1. Введите следующую команду:

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Sqlline Phoenix перестает работать после переноса кластера HBase в HDInsight 4,0

Сделайте следующее:

1. Удалите следующие таблицы Phoenix:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Если не удается удалить ни одну из таблиц, перезапустите HBase, чтобы очистить все соединения с таблицами.
1. Еще раз запустите `sqlline.py`. Phoenix повторно создаст все таблицы, которые были удалены на шаге 1.
1. Повторное создание таблиц и представлений Phoenix для данных HBase.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>Sqlline Phoenix прекращает работу после репликации метаданных HBase Phoenix из HDInsight 3,6 в 4,0

Сделайте следующее:

1. Перед выполнением репликации перейдите в целевой кластер 4,0 и выполните `sqlline.py`. Эта команда создаст таблицы Phoenix, такие `SYSTEM.MUTEX` как `SYSTEM.LOG` и, которые существуют только в 4,0.
1. Удалите следующие таблицы:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. Запуск репликации HBase

## <a name="deprecation"></a>Устаревшее

Службы Apache Storm и ML недоступны в HDInsight 4,0.
