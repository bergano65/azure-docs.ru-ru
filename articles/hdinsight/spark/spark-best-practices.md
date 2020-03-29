---
title: Лучшие практики Apache Spark на Azure HDInsight
description: Изучите лучшие практики использования Apache Spark в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71106129"
---
# <a name="apache-spark-best-practices"></a>Лучшие практики Apache Spark

В этой статье представлены различные рекомендации по использованию Apache Spark на Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Как запустить или представить вакансии Spark?

| Параметр | Документы |
|---|---|
| VSCode | [Используйте Spark & Hive Tools для визуального кода студии](../hdinsight-for-vscode.md) |
| Jupyter Notebook | [Руководство. Загрузка данных и выполнение запросов в кластере Apache Spark в Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Учебник: Используйте Azure Toolkit для IntelliJ для создания приложений Apache Spark для кластера HDInsight](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Руководство. Создание приложения Scala Maven для Apache Spark в HDInsight с помощью IntelliJ](./apache-spark-create-standalone-application.md) |
| Записные книжки Zeppelin | [Use Apache Zeppelin notebooks with Apache Spark cluster on Azure HDInsight](./apache-spark-zeppelin-notebook.md) (Использование записных книжек Apache Zeppelin с кластером Apache Spark в Azure HDInsight) |
| Удаленное представление о работе с Livy | [Удаленная отправка заданий Spark в кластер Azure HDInsight с помощью Apache Spark REST API](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Как контролировать и отлажовать задания Spark?

| Параметр | Документы |
|---|---|
| Набор средств Azure для IntelliJ | [Неудача искры отладки работы с Azure Toolkit для IntelliJ (предварительный просмотр)](apache-spark-intellij-tool-failure-debug.md) |
| Лазурный инструмент для IntelliJ через SSH | [Удаленная или локальная отладка приложений Apache Spark в кластере HDInsight с помощью набора Azure Toolkit for IntelliJ через SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Лазурный инструмент для IntelliJ через VPN | [Удаленная отладка приложений Apache Spark в HDInsight через VPN с помощью Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| График работы на сервере истории Apache Spark | [Отладка и диагностика приложений Apache Spark с использованием расширенного сервера журнала Apache Spark](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Как сделать работу spark более эффективной?

| Параметр | Документы |
|---|---|
| Кэш IO | [Повышение производительности рабочих нагрузок Apache Spark с помощью службы IO Cache для Azure HDInsight (предварительная версия)](./apache-spark-improve-performance-iocache.md) |
| Варианты настройки | [Оптимизация заданий Apache Spark](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Как подключиться к другим службам Azure?

| Параметр | Документы |
|---|---|
| Apache Hive в HDInsight | [Integrate Apache Spark and Apache Hive with the Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md) (Интеграция Apache Spark и Apache Hive с помощью соединителя хранилища Hive) |
| Apache HBase в HDInsight | [Чтение и запись данных Apache HBase с помощью Apache Spark](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka в HDInsight | [Руководство. Использование структурированной потоковой передачи Apache Spark с Apache Kafka в HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: реализация лямбда-архитектуры на платформе Azure](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Каковы мои варианты хранения?

| Параметр | Документы |
|---|---|
| Data Lake Storage 2-го поколения | [Используйте хранилище данных Azure Data Lake Gen2 с кластерами Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage 1-го поколения | [Использование Data Lake Storage 1-го поколения с кластерами Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md) |
| хранилище BLOB-объектов Azure | [Использование службы хранилища Azure с кластерами Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка параметров Apache Spark](apache-spark-settings.md)
* [Оптимизация рабочих мест Apache Spark в HDInsight](apache-spark-perf.md)
