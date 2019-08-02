---
title: Исключение OutOfMemoryError для Apache Spark в Azure HDInsight
description: Исключение OutOfMemoryError для Apache Spark в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/26/2019
ms.openlocfilehash: ac360312fdb3c4a238e6280872bc8c8b548e8544
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620991"
---
# <a name="scenario-outofmemoryerror-exception-for-apache-spark-in-azure-hdinsight"></a>Сценарий: Исключение OutOfMemoryError для Apache Spark в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании Apache Spark компонентов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблемы

Сбой приложения Apache Spark с необработанным исключением OutOfMemoryError. Может появиться сообщение об ошибке следующего вида:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

## <a name="cause"></a>Причина:

Наиболее вероятной причиной этого исключения является нехватка памяти кучи. Приложению Spark требуется достаточно памяти кучи виртуальных машин Java (ВИРТУАЛЬНОЙ машины Java) при запуске в качестве исполнителей или драйверов.

## <a name="resolution"></a>Разрешение

1. Определите максимальный объем данных, которые будет обрабатывать приложение Spark. Оценка размера в зависимости от максимального размера входных данных — промежуточных данных, полученных путем преобразования входных данных и выходных данных, созданных для преобразования промежуточных данных. Если начальная оценка недостаточна, увеличьте размер и выполните итерацию до подчасти ошибок памяти.

1. Убедитесь, что в кластере HDInsight, который должен использоваться, достаточно ресурсов, таких как память и количество ядер, для работы приложения Spark. Это можно определить, просмотрев в разделе Cluster Metrics (Метрики кластера) пользовательского интерфейса YARN кластера такие значения, как Memory Used (Используемая память) и Memory Total (Всего памяти), а также VCores Used (Используемые ядра VCore) и VCores Total (Всего ядер VCore).

    ![представление памяти Yarn Core](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Задайте для следующих конфигураций Spark соответствующие значения. Балансировка требований приложения с помощью доступных ресурсов в кластере. Эти значения не должны превышать 90% доступной памяти и ядер, отображаемых YARN, а также должны соответствовать минимальным требованиям к памяти для приложения Spark:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Общий объем памяти, используемый всеми исполнителями =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Общий объем памяти, используемый драйвером =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* [Общие сведения об управлении памятью Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Отладка приложения Spark в кластерах HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
