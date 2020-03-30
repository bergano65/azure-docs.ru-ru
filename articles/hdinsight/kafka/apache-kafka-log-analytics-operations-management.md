---
title: Журналы Azure Monitor для Apache Kafka - Azure HDInsight
description: Узнайте, как использовать журналы Azure Monitor для анализа журналов из кластера Apache Kafka в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 3f8ff3cbc24f6e3a7e0eccf1b18e01941c9584b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471186"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Анализ журналов для Apache Kafka в HDInsight

Узнайте, как использовать журналы Azure Monitor для анализа журналов, генерируемых Apache Kafka на HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Местоположение журналов

Входы Apache Kafka в кластере расположены по адресу `/var/log/kafka`. Журналы Kafka не сохраняются и не сохраняются в жизненных циклах кластеров, независимо от того, используются ли управляемые диски. В следующей таблице показаны доступные журналы.

|Журнал |Описание |
|---|---|
|kafka.out|stdout и stderr процесса Кафки. В этом файле вы найдете журналы запуска и выключения Kafka.|
|server.log|Основной журнал сервера Kafka. Все журналы брокера Кафки заканчиваются здесь.|
|controller.log|Контроллер журналы, если брокер выступает в качестве контроллера.|
|statechange.log|Все события изменения состояния для брокеров регистрируются в этом файле.|
|kafka-gc.log|Статистика сбора мусора Кафка.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Включить журналы Azure Monitor для Apache Kafka

Шаги, позволяющие включить журналы Azure Monitor для HDInsight, одинаковы для всех кластеров HDInsight. Чтобы узнать, как создать и настроить необходимые службы, воспользуйтесь ссылками ниже.

1. Создание рабочей области Log Analytics. Для получения дополнительной информации смотрите документ [журналы в документе Azure Monitor.](../../azure-monitor/platform/data-platform-logs.md)

2. Создание Kafka в кластере HDInsight. Дополнительные сведения см. в документе [Приступая к работе с Apache Kafka (предварительная версия) в HDInsight](apache-kafka-get-started.md).

3. Найдите кластер Kafka для использования журналов Azure Monitor. Для получения дополнительной [информации смотрите журналы «Используйте монитор Azure» для мониторинга документа HDInsight.](../hdinsight-hadoop-oms-log-analytics-tutorial.md)

> [!IMPORTANT]  
> Это может занять около 20 минут, прежде чем данные доступны для журналов Azure Monitor.

## <a name="query-logs"></a>Журналы запросов

1. На [портале Azure](https://portal.azure.com)выберите рабочее пространство для анализа журналов.

2. Из левого меню, под **общим**, выберите **журналы**. Здесь можно выполнять поиск данных, собранных из Kafka. Введите запрос в окне запроса, а затем выберите **Выполнить.** Ниже приведены некоторые примеры поисковых запросов:

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

* Входящие сообщения в секунду: (Заменить `your_kafka_cluster_name` имя кластера.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Входящие байты в секунду: (Заменить `wn0-kafka` имя узла рабочего узла.)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Исходящие байты в `your_kafka_cluster_name` секунду: (Заменить имя кластера.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    Кроме того, вы можете ввести `*` для поиска всех типов данных журнала. В настоящее время для запросов доступны следующие журналы:

    | Тип журнала | Описание |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Брокер Kafka, server.log |
    | log\_kafkacontroller\_CL | Брокер Kafka, controller.log |
    | metrics\_kafka\_CL | Метрики Kafka JMX |

    ![Apache kafka журнал аналитики CPU использования](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о Azure Monitor смотрите [обзор Azure Monitor](../../log-analytics/log-analytics-get-started.md)и [журналы мониторинга запросов Azure Monitor для мониторинга кластеров HDInsight.](../hdinsight-hadoop-oms-log-analytics-use-queries.md)

Дополнительные сведения о работе с Apache Kafka см. в следующих документах:

* [Зеркальное отображение Apache Kafka в кластерах HDInsight](apache-kafka-mirroring.md)
* [Увеличьте масштаб Apache Kafka на HDInsight](apache-kafka-scalability.md)
* [Использование потоковой передачи Apache Spark (DStream) с Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Использование структурированной потоковой передачи Apache Spark с Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
