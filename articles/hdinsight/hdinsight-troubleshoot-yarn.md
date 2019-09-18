---
title: Устранение неполадок с YARN в Azure HDInsight
description: Получите ответы на распространенные вопросы о работе с Apache Hadoop YARN и Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 107230a4d8c2e0427c2fcf2a8862559c707831a1
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076570"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Устранение неполадок рабочих нагрузок Apache Hadoop YARN с помощью Azure HDInsight

Ознакомьтесь с основными проблемами и их разрешением при работе с полезными данными Apache Hadoop YARN в Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Как создать очередь YARN в кластере?

### <a name="resolution-steps"></a>Способы устранения

Чтобы создать очередь YARN и выполнить балансировку выделения емкости для всех очередей, выполните следующие шаги с помощью Ambari.

В этом примере емкость двух имеющихся очередей (**default** и **thriftsvr**) изменяется с 50 % на 25 %, что позволяет обеспечить для новой очереди (spark) емкость 50 %.

| Очередь | Capacity | Максимальная емкость |
| --- | --- | --- |
| значение по умолчанию | 25% | 50 % |
| thrftsvr | 25 % | 50 % |
| spark | 50 % | 50 % |

1. Выберите значок **просмотров Ambari**, а затем выберите шаблон сетки. После этого выберите **диспетчер очереди YARN**.

    ![Выбор значка просмотров Ambari](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png)
2. Выберите очередь **по умолчанию**.

    ![Выбор очереди default](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png)
3. Для очереди **default** измените **емкость** с 50 % на 25 %. Для очереди **thriftsvr** измените **емкость** на 25 %.

    ![Изменение емкости до 25 % для очередей default и thriftsvr](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png)
4. Нажмите кнопку **Add Queue** (Добавить очередь), чтобы создать очередь.

    ![Выбор кнопки Add Queue (Добавить очередь)](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png)

5. Присвойте имя новой очереди.

    ![Имя Spark для новой очереди](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png)  

6. Оставьте значение **емкости** 50 %, а затем выберите кнопку **Действия**.

    ![Выбор кнопки "Действия"](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png)  
7. Выберите команду **Save and Refresh Queues** (Сохранить и обновить очереди).

    ![Выбор Save and Refresh Queues (Сохранить и обновить очереди)](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png)  

После применения эти изменения сразу же появятся на пользовательском интерфейсе планировщика YARN.

### <a name="additional-reading"></a>Дополнительные материалы для чтения

- [Hadoop: Capacity Scheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) (Hadoop: планировщик ресурсов)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Как скачать журналы YARN из кластера?

### <a name="resolution-steps"></a>Способы устранения 

1. Подключитесь к кластеру HDInsight с помощью клиента Secure Shell (SSH). Подробные сведения см. в разделе [Дополнительные материалы](#additional-reading-2).

1. Чтобы получить список всех идентификаторов текущих выполняемых приложений YARN, выполните следующую команду:

    ```apache
    yarn top
    ```

    Идентификаторы перечислены в столбце **APPLICATIONID**. Вы можете скачать журналы из столбца **APPLICATIONID**.

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Чтобы скачать журналы для всех основных контейнеров приложения YARN, выполните следующую команду:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Будет создан файл журнала с именем amlogs.txt.

1. Чтобы скачать журналы только для последнего основного контейнера приложения YARN, выполните следующую команду:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Будет создан файл журнала с именем latestamlogs.txt.

1. Чтобы скачать журналы для первых двух основных контейнеров приложения YARN, выполните следующую команду:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    Будет создан файл журнала с именем first2amlogs.txt.

1. Чтобы скачать все журналы контейнеров приложения YARN, выполните следующую команду:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Будет создан файл журнала с именем logs.txt.

1. Чтобы скачать журнал контейнера YARN для определенного контейнера, выполните следующую команду:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    Будет создан файл журнала с именем containerlogs.txt.

### <a name="additional-reading-2"></a>Дополнительные материалы

- [Подключение к HDInsight (Hadoop) с помощью SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [APACHE HADOOP YARN — CONCEPTS AND APPLICATIONS](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow) (Apache Hadoop YARN: приложения и основные понятия)

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

- Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

- Подключение с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

- Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
