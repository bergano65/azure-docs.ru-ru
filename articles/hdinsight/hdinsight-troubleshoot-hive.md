---
title: Устранение неполадок в Hive с помощью Azure HDInsight
description: Получите ответы на распространенные вопросы о работе с Apache Hive и Azure HDInsight.
keywords: Azure HDInsight, Hive, вопросы и ответы, руководство по устранению неполадок, часто задаваемые вопросы
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 02247adb9852a72b386feb2ef0924b0f1b3d6277
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895236"
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

Код в разделе "Способы устранения" предполагает, что пути к данным в новом кластере являются такими же, как и в старом кластере. Если пути данных отличаются, можно вручную `alltables.sql` отсеивать генерируемый файл с учетом любых изменений.

### <a name="additional-reading"></a>Дополнительные материалы

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

4. Для просмотра журналов серверов Hive используйте следующую команду:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Дополнительные материалы

- [Подключение к HDInsight (Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Как запустить оболочку Hive с определенными конфигурациями в кластере?

### <a name="resolution-steps"></a>Способы устранения

1. Укажите пару "ключ-значение" конфигурации при запуске оболочки Hive. Подробные сведения см. в разделе [Дополнительные материалы](#additional-reading-end).

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

### <a name="additional-reading"></a>Дополнительные материалы

- [Свойства конфигурации Hive](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-apache-tez-dag-data-on-a-cluster-critical-path"></a><a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Как анализировать данные направленного ациклического графа Apache Tez по критическому пути кластера?

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
    - **ContainerReuseAnalyzer** — печать сведений о повторном использовании контейнера в DAG;
    - **CriticalPath** — поиск критического пути DAG;
    - **LocalityAnalyzer** — печать сведений о местоположении в DAG;
    - **ShuffleTimeAnalyzer** — анализ сведений о смещении времени в DAG;
    - **SkewAnalyzer** — анализ сведений о неравномерном распределении данных в DAG;
    - **SlowNodeAnalyzer** — печать сведений об узле в DAG;
    - **SlowTaskIdentifier** — печать сведений о медленных задачах в DAG;
    - **SlowestVertexAnalyzer** — печать сведений о медленных вершинах в DAG;
    - **SpillAnalyzer** — печать сведений о перемещении в DAG;
    - **TaskConcurrencyAnalyzer** — печать сведений о параллельных задачах в DAG;
    - **VertexLevelCriticalPathAnalyzer** — поиск критического пути на уровне вершины в DAG.

### <a name="additional-reading"></a>Дополнительные материалы

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

### <a name="additional-reading"></a><a name="additional-reading-end"></a>Дополнительные материалы

[Подключение к HDInsight (Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

- Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

- Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

- Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
