---
title: Ноклассдеффаундеррор-Apache Spark с данными Apache Kafka в Azure HDInsight
description: Задание потоковой передачи Apache Spark, считывающее данные из кластера Apache Kafka, завершается сбоем с Ноклассдеффаундеррор в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: b7cbf4d460f872f33c447d0eea8430916d65bd18
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736284"
---
# <a name="scenario-apache-spark-streaming-job-that-reads-data-from-an-apache-kafka-cluster-fails-with-a-noclassdeffounderror-in-azure-hdinsight"></a>Сценарий. Задание потоковой передачи Apache Spark, считывающее данные из кластера Apache Kafka, завершается сбоем с Ноклассдеффаундеррор в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании Apache Spark компонентов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблемы

Кластер Apache Spark запускает задание потоковой передачи Spark, которое считывает данные из кластера Apache Kafka. Задание потоковой передачи Spark завершается ошибкой, если включено сжатие потоков Kafka. В этом случае сбой application_1525986016285_0193 приложения потоковой передачи Spark Yarn из-за ошибки:

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>Причина:

Эта ошибка может быть вызвана указанием версии `spark-streaming-kafka` JAR-файла, который отличается от версии кластера Kafka, который вы используете.

Например, если вы используете кластер Kafka версии 0.10.1, следующая команда приведет к ошибке:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Разрешение

Используйте команду Spark-Submit с `–packages` параметром и убедитесь, что версия файла JAR-Streaming-Kafka совпадает с версией работающего кластера Kafka.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
