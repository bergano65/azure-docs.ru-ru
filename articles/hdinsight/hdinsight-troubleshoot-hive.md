---
title: Устранение неполадок в Hive с помощью Azure HDInsight
description: Получите ответы на распространенные вопросы о работе с Apache Hive и Azure HDInsight.
keywords: Azure HDInsight, Hive, вопросы и ответы, руководство по устранению неполадок, часто задаваемые вопросы
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: ca1e3e11ad5458e8e7f7072b7d3dd561853029fe
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575695"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Устранение неполадок в Apache Hive с помощью Azure HDInsight

Ознакомьтесь с основными проблемами и их разрешением при работе с полезными данными Apache Hive в Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Как экспортировать хранилище метаданных Hive и импортировать его в другой кластер?

### <a name="resolution-steps"></a>Способы устранения

1. Подключитесь к кластеру HDInsight с помощью клиента Secure Shell (SSH). Подробные сведения см. в разделе [Дополнительные материалы](#additional-reading-end).

2. Выполните следующую команду в кластере HDInsight, из которого вы хотите экспортировать хранилище метаданных:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Эта команда создает файл с именем allatables.sql.

3. Скопируйте файл alltables.sql в новый кластер HDInsight, а затем выполните следующую команду:

    ```apache
    hive -f alltables.sql
    ```

Код в разделе "Способы устранения" предполагает, что пути к данным в новом кластере являются такими же, как и в старом кластере. Если пути к данным различаются, можно вручную изменить созданный `alltables.sql` файл, чтобы отразить любые изменения.

### <a name="additional-reading"></a>Дополнительные материалы для чтения

- [Подключение к HDInsight (Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Как найти журналы Hive в кластере?

### <a name="resolution-steps"></a>Способы устранения

1. Подключитесь к кластеру HDInsight с помощью протокола SSH. Подробные сведения см. в разделе **Дополнительные материалы**.

2. Чтобы просмотреть журналы клиента Hive, используйте следующую команду:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Чтобы просмотреть журналы хранилища метаданных Hive, используйте следующую команду:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. Чтобы просмотреть журналы сервера Hive, используйте следующую команду:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Дополнительные материалы для чтения

- [Подключение к HDInsight (Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Как запустить оболочку Hive с определенными конфигурациями в кластере?

### <a name="resolution-steps"></a>Способы устранения

1. Укажите пару "ключ-значение" конфигурации при запуске оболочки Hive. Дополнительные сведения см. в разделе [Дополнительные материалы](#additional-reading-end).

   ```apache
   hive -hiveconf a=b
   ```

2. Чтобы получить список всех действующих конфигураций оболочки Hive, используйте следующую команду:

   ```apache
   hive> set;
   ```

   Например, используйте следующую команду для запуска оболочки Hive, включив ведение журнала отладки в консоли:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>Дополнительные материалы для чтения

- [Свойства конфигурации Hive](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Как анализировать данные направленного ациклического графа Apache Tez по критическому пути кластера?

### <a name="resolution-steps"></a>Способы устранения

1. Чтобы проанализировать направленный ациклический граф (DAG) Apache Tez по критическому графу кластера, подключитесь к кластеру HDInsight с помощью SSH. Подробные сведения см. в разделе [Дополнительные материалы](#additional-reading-end).

2. В командной строке выполните следующую команду:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Чтобы получить список других анализаторов, которые можно использовать для анализа данных направленного ациклического графа Tez, воспользуйтесь следующей командой:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Необходимо задать пример программы в качестве первого аргумента.

   Допустимые имена программы:
    - **ContainerReuseAnalyzer**: печать сведений о повторном использовании контейнера в DAG;
    - **CriticalPath**: поиск критического пути DAG;
    - **LocalityAnalyzer**: печать сведений о местоположении в DAG;
    - **ShuffleTimeAnalyzer**: анализ сведений о смещении времени в DAG;
    - **SkewAnalyzer**: анализ сведений о неравномерном распределении данных в DAG;
    - **SlowNodeAnalyzer**: печать сведений об узле в DAG;
    - **SlowTaskIdentifier**: печать сведений о медленных задачах в DAG;
    - **SlowestVertexAnalyzer**: печать сведений о медленных вершинах в DAG;
    - **SpillAnalyzer**: печать сведений о перемещении в DAG;
    - **TaskConcurrencyAnalyzer**: печать сведений о параллельных задачах в DAG;
    - **VertexLevelCriticalPathAnalyzer**: поиск критического пути на уровне вершины в DAG.

### <a name="additional-reading"></a>Дополнительные материалы для чтения

- [Подключение к HDInsight (Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Как скачать данные направленного ациклического графа Tez из кластера?

#### <a name="resolution-steps"></a>Способы устранения

Сбор данных направленного ациклического графа Tez можно выполнить двумя способами:

- Из командной строки.

    Подключитесь к кластеру HDInsight с помощью протокола SSH. В командной строке выполните следующую команду:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Использовать представление Ambari Tez.

  1. Перейдите в Ambari.
  2. Выберите представление Tez (под значком плитки в правом верхнем углу).
  3. Выберите направленный ациклический граф, который необходимо просмотреть.
  4. Выберите **Скачать данные**.

### <a name="additional-reading-end"></a>Дополнительные материалы

[Подключение к HDInsight (Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

- Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

- Подключение с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

- Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
