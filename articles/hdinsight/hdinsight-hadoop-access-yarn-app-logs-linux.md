---
title: Доступ к журналам приложений Apache Hadoop YARN - Azure HDInsight
description: Узнайте, как получить доступ к журналам приложений YARN в кластере HDInsight (Apache Hadoop) под управлением Linux с помощью как командной строки, так и веб-браузера.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/23/2020
ms.openlocfilehash: 2a7d71c6d751d4a48ec93f020e657a4d43114cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764383"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Доступ к журналам приложений Apache Hadoop YARN в HDInsight под управлением Linux

Узнайте, как получить доступ к журналам приложений [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Yet Another Resource Negotiator) в кластере [Apache Hadoop](https://hadoop.apache.org/) в Azure HDInsight.

## <a name="what-is-apache-yarn"></a>Что такое Apache YARN?

YARN поддерживает несколько моделей программирования (в том числе [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html)), отделяя управление ресурсами от планирования и мониторинга приложений. YARN использует глобальный диспетчер *ResourceManager*, *диспетчеры узлов* на каждый рабочий узел и *диспетчеры приложений* на каждое приложение. Диспетчер приложений согласовывает ресурсы (ЦП, память, диск, сеть), необходимые для работы приложения, с диспетчером ресурсов. Диспетчер ресурсов совместно с диспетчером узлов предоставляют эти ресурсы в виде *контейнеров*. Диспетчер приложений отвечает за отслеживание хода выполнения контейнеров, назначаемых ему диспетчером ресурсов. Приложению может потребоваться много контейнеров в зависимости от характера приложения.

Для выполнения приложения может потребоваться несколько *попыток*. Если выполнение приложения завершается ошибкой, его можно повторить как новую попытку. Каждая попытка выполняется в контейнере. В некотором смысле контейнер обеспечивает контекст основной единице работы, выполняемой приложением YARN, и вся работа, выполняемая в контексте контейнера, выполняется на одном рабочем узле, где был выделен контейнер. Смотрите [Hadoop: Написание приложений YARN](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html), или [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) для дальнейшей справки.

Для масштабирования кластера для поддержки большей пропускной мощности обработки можно использовать [Autoscale](hdinsight-autoscale-clusters.md) или [масштабировать кластеры вручную, используя несколько различных языков.](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters)

## <a name="yarn-timeline-server"></a>Сервер временной шкалы YARN

[Apache Hadoop YARN Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) предоставляет общие сведения о приложениях, завершивших работу.

YARN Timeline Server содержит следующие типы данных:

* уникальный идентификатор приложения;
* имя пользователя, запустившего приложение;
* информация о попытках завершить приложение;
* информация о контейнерах, которые использовались во время каждой из попыток.

## <a name="yarn-applications-and-logs"></a>Приложения и журналы YARN

YARN поддерживает несколько моделей программирования (в том числе [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html)), отделяя управление ресурсами от планирования и мониторинга приложений. YARN использует глобальный диспетчер *ResourceManager*, *диспетчеры узлов* на каждый рабочий узел и *диспетчеры приложений* на каждое приложение. Диспетчер приложений согласовывает ресурсы (ЦП, память, диск, сеть), необходимые для работы приложения, с диспетчером ресурсов. Диспетчер ресурсов совместно с диспетчером узлов предоставляют эти ресурсы в виде *контейнеров*. Диспетчер приложений отвечает за отслеживание хода выполнения контейнеров, назначаемых ему диспетчером ресурсов. Приложению может потребоваться много контейнеров в зависимости от характера приложения.

Для выполнения приложения может потребоваться несколько *попыток*. Если выполнение приложения завершается ошибкой, его можно повторить как новую попытку. Каждая попытка выполняется в контейнере. В некотором смысле контейнер обеспечивает контекст основной единице работы, выполняемой приложением YARN, и вся работа, выполняемая в контексте контейнера, выполняется на одном рабочем узле, где был выделен контейнер. Дополнительные сведения см. в статье об [основных понятиях Apache Hadoop YARN](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html).

Журналы приложений (и соответствующие журналы контейнеров) крайне важны для отладки проблемных приложений Hadoop. YARN предоставляет хорошую платформу для сбора, объединения и хранения журналов приложений с функцией [объединения журналов](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/). Функция объединения журналов делает доступ к журналам приложений более детерминированным, так как она объединяет журналы со всех контейнеров на рабочем узле и хранит их как один сводный файл журнала на рабочем узле в файловой системе по умолчанию после завершения приложения. Ваше приложение может использовать сотни или тысячи контейнеров, но журналы для всех контейнеров, выполненных на одном рабочем узле, всегда объединяются в один файл. Таким образом, на рабочий узла используется только 1 журнал, используемый вашим приложением. Объединение журналов включено по умолчанию на кластерах HDInsight версии 3.0 и более поздних версий. Объединенные журналы находятся в хранилище по умолчанию для кластера. В качестве пути HDFS к журналам используется следующий путь:

```
/app-logs/<user>/logs/<applicationId>
```

В пути `user` — это имя пользователя, запустившего приложение. `applicationId` является уникальным идентификатором, назначенным YARN RM приложению.

Агрегированные журналы не читаемы напрямую, так как они записаны в [TFile,](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf) [двоичном формате,](https://issues.apache.org/jira/browse/HADOOP-3315) индексируемом контейнером. Чтобы отобразить эти журналы для интересующих вас приложений или контейнеров в виде обычного текста, используйте журналы YARN ResourceManager или средства CLI.

## <a name="yarn-logs-in-an-esp-cluster"></a>Займы в кластере ESP

Две конфигурации должны быть `mapred-site` добавлены к обычаю в Ambari.

1. Из веб-браузера, `https://CLUSTERNAME.azurehdinsight.net`перейдите к , где `CLUSTERNAME` имя вашего кластера.

1. От пользовательского итоговый ими Ambari перейдите на **MapReduce2** > **Configs** > **Advanced** > Custom**mapred-site.**

1. Добавьте *один из* следующих наборов свойств:

    **Комплект 1**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **Комплект 2**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Сохраняйте изменения и перезапускают все затронутые службы.

## <a name="yarn-cli-tools"></a>Средства CLI для YARN

1. Используйте [команду ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) для подключения к кластеру. Отоверьте приведенную ниже команду, заменив CLUSTERNAME на имя кластера, а затем введите команду:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Перечислите все идентификаторы приложений, работающих в настоящее время в приложениях Yarn, со следующей командой:

    ```bash
    yarn top
    ```

    Обратите внимание на `APPLICATIONID` идентификатор приложения из столбца, журналы которого должны быть загружены.

    ```output
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

1. Эти журналы можно отобразить в виде обычного текста, запустив одну из указанных ниже команд.

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    При выполнении этих команд необходимо указать параметры &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId> и &lt;worker-node-address>.

### <a name="other-sample-commands"></a>Другие выборочные команды

1. Скачать журналы контейнеров Yarn для всех мастеров приложений с командой ниже. Будет создан файл журнала с именем `amlogs.txt` в текстовом формате.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Скачайте журналы только для последнего основного контейнера приложения Yarn с помощью следующей команды:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Скачайте журналы для первых двух основных контейнеров приложения Yarn с помощью следующей команды:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Скачайте все журналы контейнеров приложения Yarn с помощью следующей команды:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Скачайте журнал контейнера Yarn для определенного контейнера с помощью следующей команды:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>Пользовательский интерфейс YARN ResourceManager

Пользовательский интерфейс YARN ResourceManager работает на головном узле кластера. Доступ к нему осуществляется через веб-ui Ambari. Чтобы просмотреть журналы YARN, выполните следующие действия:

1. В браузере перейдите по адресу `https://CLUSTERNAME.azurehdinsight.net`. Параметр CLUSTERNAME нужно заменить именем кластера HDInsight.

2. Из списка услуг слева выберите **YARN.**

    ![Apache Ambari Yarn сервис выбран](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. В раскрывающемся списке **Быстрые ссылки** выберите один из главных узлов кластера, а затем щелкните **Журнал Resource Manager**.

    ![Apache Ambari пряжи быстрые ссылки](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Вам представлен список ссылок на журналы YARN.
