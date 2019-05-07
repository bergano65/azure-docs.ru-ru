---
title: Журналы Azure Monitor для Apache Kafka — Azure HDInsight
description: Узнайте, как использовать журналы Azure Monitor для анализа журналов из кластера Apache Kafka в Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 1e141aea3b22bfdcb981513f03e595b6c2f15466
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147971"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Анализ журналов для Apache Kafka в HDInsight

Узнайте, как использовать журналы Azure Monitor для анализа журналов, созданных в Apache Kafka в HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Включить журналы Azure Monitor для Apache Kafka

Действия, чтобы включить журналы Azure Monitor для HDInsight одинаковы для всех кластеров HDInsight. Чтобы узнать, как создать и настроить необходимые службы, воспользуйтесь ссылками ниже.

1. Создание рабочей области Log Analytics. Дополнительные сведения см. в разделе [журналы в Azure Monitor](../../azure-monitor/platform/data-platform-logs.md) документа.

2. Создание Kafka в кластере HDInsight. Дополнительные сведения см. в документе [Приступая к работе с Apache Kafka (предварительная версия) в HDInsight](apache-kafka-get-started.md).

3. Настройка кластера Kafka и использовать журналы Azure Monitor. Дополнительные сведения см. в разделе [журналов использования Azure Monitor для мониторинга HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) документа.

> [!IMPORTANT]  
> Может занять около 20 минут, прежде чем данные будут доступны для журналов Azure Monitor.

## <a name="query-logs"></a>Журналы запросов

1. На [портале Azure](https://portal.azure.com) выберите рабочую область Log Analytics.

2. В меню слева в разделе **Общие**выберите **журналы**. Здесь можно выполнять поиск данных, собранных из Kafka. Введите запрос в окне запроса, а затем выберите **запуска**. Ниже приведены некоторые примеры поисковых запросов:

* Использование дискового пространства:

    ```kusto
    Perf 
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Загрузка ЦП:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Входящих сообщений в секунду:

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Входящих байт в секунду:

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Исходящих байт в секунду:

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    > [!IMPORTANT]  
    > Замените значения запроса своими сведениями об определенном кластере. Например, для параметра `ClusterName_s` укажите имя кластера. `HostName_s` должно быть присвоено доменное имя рабочего узла в кластере.
    
    Кроме того, вы можете ввести `*` для поиска всех типов данных журнала. В настоящее время для запросов доступны следующие журналы:
    
    | Тип журнала | ОПИСАНИЕ |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Брокер Kafka, server.log |
    | log\_kafkacontroller\_CL | Брокер Kafka, controller.log |
    | metrics\_kafka\_CL | Метрики Kafka JMX |
    
    ![Окно поиска сведений об использовании ЦП](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure Monitor, см. в разделе [Обзор Azure Monitor](../../log-analytics/log-analytics-get-started.md), и [журналы запросов Azure Monitor для мониторинга кластеров HDInsight](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Дополнительные сведения о работе с Apache Kafka см. в следующих документах:

* [Зеркальное отображение Apache Kafka в кластерах HDInsight](apache-kafka-mirroring.md)
* [Повышение уровня масштабируемости Apache Kafka в HDInsight](apache-kafka-scalability.md)
* [Использование потоковой передачи Apache Spark (DStream) с Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Использование структурированной потоковой передачи Apache Spark с Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
