---
title: Журналы Azure Monitor для Apache Kafka — Azure HDInsight
description: Узнайте, как использовать журналы Azure Monitor для анализа журналов из кластера Apache Kafka в Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 281b4b8d20957cbbbf0f4ff52166e8c3a78b3e7d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108143"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Анализ журналов для Apache Kafka в HDInsight

Узнайте, как использовать журналы Azure Monitor для анализа журналов, созданных в Apache Kafka в HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Включить журналы Azure Monitor для Apache Kafka

Действия, чтобы включить журналы Azure Monitor для HDInsight одинаковы для всех кластеров HDInsight. Чтобы узнать, как создать и настроить необходимые службы, воспользуйтесь ссылками ниже.

1. Создание рабочей области Log Analytics. Дополнительные сведения см. в документе [Начало работы с рабочей областью Log Analytics](https://docs.microsoft.com/azure/log-analytics).

2. Создание Kafka в кластере HDInsight. Дополнительные сведения см. в документе [Приступая к работе с Apache Kafka (предварительная версия) в HDInsight](apache-kafka-get-started.md).

3. Настройка кластера Kafka и использовать журналы Azure Monitor. Дополнительные сведения см. в разделе [журналов использования Azure Monitor для мониторинга HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) документа.

    > [!NOTE]  
    > Можно также настроить кластер для использования Azure Monitor журналы с помощью `Enable-AzureRmHDInsightOperationsManagementSuite` командлета. Для использования этого командлета требуется указанная ниже информация.
    >
    > * Имя кластера HDInsight.
    > * Идентификатор рабочей области для журналов Azure Monitor. Идентификатор рабочей области можно найти в рабочей области Log Analytics.
    > * Первичный ключ для подключения к log analytics. Чтобы найти первичный ключ, откройте рабочую область на портале Azure и выберите __Дополнительные параметры__ в меню слева. В разделе "Дополнительные параметры" последовательно выберите __Подключенные источники__>__Серверы с Linux__.


> [!IMPORTANT]  
> Может занять около 20 минут, прежде чем данные будут доступны для журналов Azure Monitor.

## <a name="query-logs"></a>Журналы запросов

1. На [портале Azure](https://portal.azure.com) выберите рабочую область Log Analytics.

2. Выберите __Поиск по журналам__. Здесь можно выполнять поиск данных, собранных из Kafka. Ниже приведены некоторые примеры поисковых запросов:

   * Использование дискового пространства: `Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

   * Загрузка ЦП: `Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

   * Входящих сообщений в секунду: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

   * Входящих байт в секунду: `metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

   * Исходящих байт в секунду: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

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

Дополнительные сведения о Azure Monitor, см. в разделе [Обзор Azure Monitor](../../log-analytics/log-analytics-get-started.md) документа.

Дополнительные сведения о работе с Apache Kafka см. в следующих документах:

 * [Зеркальное отображение Apache Kafka в кластерах HDInsight](apache-kafka-mirroring.md)
 * [Повышение уровня масштабируемости Apache Kafka в HDInsight](apache-kafka-scalability.md)
 * [Использование потоковой передачи Apache Spark (DStream) с Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
 * [Использование структурированной потоковой передачи Apache Spark с Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
