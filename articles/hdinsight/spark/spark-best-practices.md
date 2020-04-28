---
title: Apache Spark рекомендации по Azure HDInsight
description: Ознакомьтесь с рекомендациями по использованию Apache Spark в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "71106129"
---
# <a name="apache-spark-best-practices"></a>Рекомендации по Apache Spark

В этой статье приводятся различные рекомендации по использованию Apache Spark в Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Разделы справки запускать или отправлять задания Spark?

| Параметр | Документы |
|---|---|
| VSCode | [Использование Spark & средств Hive для Visual Studio Code](../hdinsight-for-vscode.md) |
| Jupyter Notebook | [Руководство. Загрузка данных и выполнение запросов в кластере Apache Spark в Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Учебник. Использование Azure Toolkit for IntelliJ для создания Apache Spark приложений для кластера HDInsight](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Руководство по Создание приложения Scala Maven для Apache Spark в HDInsight с помощью IntelliJ](./apache-spark-create-standalone-application.md) |
| Записные книжки Zeppelin | [Use Apache Zeppelin notebooks with Apache Spark cluster on Azure HDInsight](./apache-spark-zeppelin-notebook.md) (Использование записных книжек Apache Zeppelin с кластером Apache Spark в Azure HDInsight) |
| Отправка удаленных заданий с помощью Livy | [Удаленная отправка заданий Spark в кластер Azure HDInsight с помощью Apache Spark REST API](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Разделы справки отслеживать задания Spark и выполнять их отладку?

| Параметр | Документы |
|---|---|
| Набор средств Azure для IntelliJ | [Сбой отладки задания Spark с Azure Toolkit for IntelliJ (Предварительная версия)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit for IntelliJ через SSH | [Удаленная или локальная отладка приложений Apache Spark в кластере HDInsight с помощью набора Azure Toolkit for IntelliJ через SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit for IntelliJ через VPN | [Удаленная отладка приложений Apache Spark в HDInsight через VPN с помощью Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Граф заданий на Apache Spark сервере журнала | [Отладка и диагностика приложений Apache Spark с использованием расширенного сервера журнала Apache Spark](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Разделы справки сделать мои задания Spark более эффективными?

| Параметр | Документы |
|---|---|
| Кэш ввода-вывода | [Повышение производительности рабочих нагрузок Apache Spark с помощью службы IO Cache для Azure HDInsight (предварительная версия)](./apache-spark-improve-performance-iocache.md) |
| Параметры конфигурации | [Оптимизация заданий Apache Spark](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Разделы справки подключиться к другим службам Azure?

| Параметр | Документы |
|---|---|
| Apache Hive в HDInsight | [Integrate Apache Spark and Apache Hive with the Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md) (Интеграция Apache Spark и Apache Hive с помощью соединителя хранилища Hive) |
| Apache HBase в HDInsight | [Чтение и запись данных Apache HBase с помощью Apache Spark](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka в HDInsight | [Руководство по Использование структурированной потоковой передачи Apache Spark с Apache Kafka в HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: реализация лямбда-архитектуры на платформе Azure](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Что такое мои параметры хранения?

| Параметр | Документы |
|---|---|
| Data Lake Storage 2-го поколения | [Использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage 1-го поколения | [Использование Data Lake Storage 1-го поколения с кластерами Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md) |
| хранилище BLOB-объектов Azure | [Использование службы хранилища Azure с кластерами Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка параметров Apache Spark](apache-spark-settings.md)
* [Оптимизация заданий Apache Spark в HDInsight](apache-spark-perf.md)
