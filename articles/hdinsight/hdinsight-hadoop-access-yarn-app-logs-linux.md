---
title: Access Apache Hadoop YARN application logs - Azure HDInsight
description: Узнайте, как получить доступ к журналам приложений YARN в кластере HDInsight (Apache Hadoop) под управлением Linux с помощью как командной строки, так и веб-браузера.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 883e1007b35991c1f5d8f0c6e949efcb48c27a1d
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327228"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Доступ к журналам приложений Apache Hadoop YARN в HDInsight под управлением Linux

Узнайте, как получить доступ к журналам приложений [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Yet Another Resource Negotiator) в кластере [Apache Hadoop](https://hadoop.apache.org/) в Azure HDInsight.

## <a name="what-is-apache-yarn"></a>What is Apache YARN?

YARN поддерживает несколько моделей программирования (в том числе [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html)), отделяя управление ресурсами от планирования и мониторинга приложений. YARN использует глобальный диспетчер *ResourceManager*, *диспетчеры узлов* на каждый рабочий узел и *диспетчеры приложений* на каждое приложение. Диспетчер приложений согласовывает ресурсы (ЦП, память, диск, сеть), необходимые для работы приложения, с диспетчером ресурсов. Диспетчер ресурсов совместно с диспетчером узлов предоставляют эти ресурсы в виде *контейнеров*. Диспетчер приложений отвечает за отслеживание хода выполнения контейнеров, назначаемых ему диспетчером ресурсов. Приложению может потребоваться много контейнеров в зависимости от характера приложения.

Для выполнения приложения может потребоваться несколько *попыток*. Если выполнение приложения завершается ошибкой, его можно повторить как новую попытку. Каждая попытка выполняется в контейнере. В некотором смысле контейнер обеспечивает контекст основной единице работы, выполняемой приложением YARN, и вся работа, выполняемая в контексте контейнера, выполняется на одном рабочем узле, где был выделен контейнер. See [Hadoop: Writing YARN Applications](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html), or [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) for further reference.

To scale your cluster to support greater processing throughput, you can use [Autoscale](hdinsight-autoscale-clusters.md) or [Scale your clusters manually using a few different languages](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters).

## <a name="YARNTimelineServer"></a>YARN Timeline Server

[Apache Hadoop YARN Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) предоставляет общие сведения о приложениях, завершивших работу.

YARN Timeline Server содержит следующие типы данных:

* уникальный идентификатор приложения;
* имя пользователя, запустившего приложение;
* информация о попытках завершить приложение;
* информация о контейнерах, которые использовались во время каждой из попыток.

## <a name="YARNAppsAndLogs"></a>Приложения и журналы YARN

YARN поддерживает несколько моделей программирования (в том числе [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html)), отделяя управление ресурсами от планирования и мониторинга приложений. YARN использует глобальный диспетчер *ResourceManager*, *диспетчеры узлов* на каждый рабочий узел и *диспетчеры приложений* на каждое приложение. Диспетчер приложений согласовывает ресурсы (ЦП, память, диск, сеть), необходимые для работы приложения, с диспетчером ресурсов. Диспетчер ресурсов совместно с диспетчером узлов предоставляют эти ресурсы в виде *контейнеров*. Диспетчер приложений отвечает за отслеживание хода выполнения контейнеров, назначаемых ему диспетчером ресурсов. Приложению может потребоваться много контейнеров в зависимости от характера приложения.

Для выполнения приложения может потребоваться несколько *попыток*. Если выполнение приложения завершается ошибкой, его можно повторить как новую попытку. Каждая попытка выполняется в контейнере. В некотором смысле контейнер обеспечивает контекст основной единице работы, выполняемой приложением YARN, и вся работа, выполняемая в контексте контейнера, выполняется на одном рабочем узле, где был выделен контейнер. See [Apache Hadoop YARN Concepts](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) for further reference.

Журналы приложений (и соответствующие журналы контейнеров) крайне важны для отладки проблемных приложений Hadoop. YARN provides a nice framework for collecting, aggregating, and storing application logs with the [Log Aggregation](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) feature. Функция объединения журналов делает доступ к журналам приложений более детерминированным, так как она объединяет журналы со всех контейнеров на рабочем узле и хранит их как один сводный файл журнала на рабочем узле в файловой системе по умолчанию после завершения приложения. Ваше приложение может использовать сотни или тысячи контейнеров, но журналы для всех контейнеров, выполненных на одном рабочем узле, всегда объединяются в один файл. So there's only 1 log per worker node used by your application. Объединение журналов включено по умолчанию на кластерах HDInsight версии 3.0 и более поздних версий. Объединенные журналы находятся в хранилище по умолчанию для кластера. В качестве пути HDFS к журналам используется следующий путь:

    /app-logs/<user>/logs/<applicationId>

В пути `user` — это имя пользователя, запустившего приложение. `applicationId` является уникальным идентификатором, назначенным YARN RM приложению.

The aggregated logs aren't directly readable, as they're written in a [TFile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf), [binary format](https://issues.apache.org/jira/browse/HADOOP-3315) indexed by container. Чтобы отобразить эти журналы для интересующих вас приложений или контейнеров в виде обычного текста, используйте журналы YARN ResourceManager или средства CLI.

## <a name="yarn-cli-tools"></a>Средства CLI для YARN

Чтобы использовать инструменты интерфейса командной строки для YARN, необходимо сначала подключиться к кластеру HDInsight с помощью SSH. См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Эти журналы можно отобразить в виде обычного текста, запустив одну из указанных ниже команд.

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

При выполнении этих команд необходимо указать параметры &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId> и &lt;worker-node-address>.

## <a name="yarn-resourcemanager-ui"></a>Пользовательский интерфейс YARN ResourceManager

Пользовательский интерфейс YARN ResourceManager работает на головном узле кластера. It's accessed through the Ambari web UI. Чтобы просмотреть журналы YARN, выполните следующие действия:

1. В браузере перейдите по адресу https://CLUSTERNAME.azurehdinsight.net. Параметр CLUSTERNAME нужно заменить именем кластера HDInsight.
2. В списке служб в левой части страницы выберите **YARN**.

    ![Apache Ambari Yarn service selected](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. В раскрывающемся списке **Быстрые ссылки** выберите один из главных узлов кластера, а затем щелкните **Журнал Resource Manager**.

    ![Apache Ambari Yarn quick links](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    You're presented with a list of links to YARN logs.
