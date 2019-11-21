---
title: Развертывание и администрирование топологий Apache Storm в Azure HDInsight
description: Learn how to deploy, monitor, and manage Apache Storm topologies using the Storm Dashboard on Linux-based HDInsight. Использование инструментов Hadoop для Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 82c5db4f75f131ebdc2434955108e7d50237d9ba
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228928"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Развертывание и администрирование топологий Apache Storm в Azure HDInsight 

С помощью этого документа вы ознакомитесь с основами управления и мониторинга топологий [Apache Storm](https://storm.apache.org/), работающих в Storm в кластерах HDInsight.

## <a name="prerequisites"></a>Технические условия

* Кластер Apache Storm в HDInsight. Ознакомьтесь со статьей [Create Linux-based clusters in HDInsight by using the Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md) (Создание кластеров под управлением Linux в HDInsight с помощью портала Azure) и выберите **Storm** для параметра **Тип кластера**.

* (Optional) Familiarity with Secure Shell (SSH) and Secure Copy (SCP). Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Optional) Visual Studio, Azure SDK 2.5.1 or newer, and the Data Lake Tools for Visual Studio. For more information, see [Apache Hadoop & Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Submit a topology using Visual Studio

You can use the Data Lake Tools for Visual Studio to submit C# or hybrid topologies to your Storm cluster. Далее используется пример приложения. For information about topology creation using the Data Lake Tools, see [Apache Storm topologies with Visual Studio and C#](apache-storm-develop-csharp-visual-studio-topology.md).

1. If you haven't already installed the latest version of the Data Lake tools for Visual Studio, see [Use Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Средства Data Lake для Visual Studio ранее назывались средствами HDInsight для Visual Studio.
    >
    > Data Lake Tools for Visual Studio are included in the **Azure Workload** for Visual Studio 2019.

2. Откройте Visual Studio.

3. In the **Start** window, select **Create a new project**.

4. In the **Create a new project** window, select the search box, and enter *Storm*. Then choose **Storm Sample** from the result list and select **Next**.

5. In the **Configure your new project** window, enter a **Project name**, and go to or create a **Location** to save the new project in. Щелкните **Создать**.

    ![Configure your new project window, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

6. In **Solution Explorer**, right-click the project, and choose **Submit to Storm on HDInsight**.

    > [!NOTE]  
    > При появлении запроса введите учетные данные входа для своей подписки Azure. Если у вас несколько подписок, воспользуйтесь той, что содержит ваш кластер Storm в HDInsight.

7. In the **Submit Topology** dialog box, under the **Storm Cluster** drop-down list, choose your Storm on HDInsight cluster, and then select **Submit**. You can monitor whether the submission is successful by viewing the **Output** pane.

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Submit a topology using SSH and the Storm command

To submit a topology to Storm using SSH:

1. Подключитесь к кластеру HDInsight с помощью SSH. Replace `USERNAME` with the name of your SSH user name (such as *sshuser*). а `CLUSTERNAME` — именем кластера HDInsight.

    ```shell
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    For more information on using SSH to connect to your HDInsight cluster, see [Connect to HDInsight (Apache Hadoop) using SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use the following command to start the *WordCount* example topology:

    ```ssh
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Эта команда запускает пример топологии WordCount в кластере. Эта топология произвольно создает предложения, а затем подсчитывает число вхождений каждого слова в предложениях.

    > [!NOTE]  
    > When submitting topology to the cluster, you must first copy the .jar file containing the cluster before using the `storm` command. Чтобы скопировать файл в кластер, можно использовать команду `scp`. Например, введите `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > The *WordCount* example, and other storm starter examples, are already included on your cluster at `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Submit a topology programmatically

Топологию можно развернуть программными средствами с помощью службы Nimbus. По адресу [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) приведен пример приложения Java, в котором показано, как развернуть и запустить топологию с помощью службы Nimbus.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Monitor and manage a topology in Visual Studio

When you submit a topology using Visual Studio, the **Storm Topologies View** window appears. Выберите топологию в списке, чтобы просмотреть информацию о выполняемой топологии.

![Monitor topology, Storm Topologies View window, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> **Топологии Storm** можно также просмотреть в **обозревателе сервера**. Разверните узлы **Azure** > **HDInsight**, щелкните правой кнопкой мыши элемент "Storm" в кластере HDInsight и выберите пункт **Просмотреть топологии Storm**.

Выделите компонент spout или bolt, чтобы просмотреть информацию о нем. A tooltip with component information appears for the item selected.

### <a name="deactivate-and-reactivate-a-topology"></a>Deactivate and reactivate a topology

Deactivating a topology pauses it until the topology is killed or reactivated. To do these operations, use the **Deactivate** and **Reactivate** buttons in the **Actions** area at the top of the **Storm Topologies View** window.

### <a name="rebalance-a-topology"></a>Rebalance a topology

Повторная балансировка топологии позволяет системе пересмотреть параллелизм топологии. For example, if you've resized the cluster to add more notes, rebalancing allows a topology to see the new nodes.

To rebalance a topology, use the **Rebalance** button in the **Actions** area of the **Storm Topologies View** window.

> [!WARNING]  
> Rebalancing a topology deactivates the topology, redistributes workers evenly across the cluster, and then returns the topology to the state it was in before rebalancing occurred. If the topology was active, it becomes active again. If the topology was deactivated, it remains deactivated.

### <a name="kill-a-running-topology"></a>Завершение запущенной топологии

Storm topologies continue running until they're stopped or the cluster is deleted. To stop a topology, use the **Kill** button in the **Actions** area.

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Monitor and manage a topology using SSH and the Storm command

Программа `storm` позволяет работать с запущенными топологиями из командной строки. Чтобы вывести полный список команд, воспользуйтесь `storm -h` .

### <a name="list-topologies"></a>Вывод списка топологий

Чтобы просмотреть список всех запущенных топологий, используйте следующую команду:

```shell
storm list
```

Эта команда возвращает сведения аналогичные следующим:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Deactivate and reactivate a topology

Deactivating a topology pauses it until the topology is killed or reactivated. Use the following commands to deactivate or reactivate:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Завершение запущенной топологии

Топологии Storm после запуска продолжают работать до тех пор, пока не будут остановлены. Для завершения топологии введите следующую команду:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Rebalance a topology

Повторная балансировка топологии позволяет системе пересмотреть параллелизм топологии. For example, if you've resized the cluster to add more notes, rebalancing allows a topology to see the new nodes.

> [!WARNING]  
> Rebalancing a topology deactivates the topology, redistributes workers evenly across the cluster, and then returns the topology to the state it was in before rebalancing occurred. If the topology was active, it becomes active again. Если она была отключена, то останется таковой.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Monitor and manage a topology using the Storm UI

The Storm UI provides a web interface for working with running topologies, and it's included on your HDInsight cluster. To view the Storm UI, use a web browser to open `https://CLUSTERNAME.azurehdinsight.net/stormui`, where *CLUSTERNAME* is the name of your cluster.

> [!NOTE]  
> If you're asked to provide a user name and password, enter the cluster administrator username and password that you used when creating the cluster.

### <a name="storm-ui-main-page"></a>Storm UI main page

На главной странице пользовательского интерфейса Storm отображается следующая информация.

| Section | Описание |
| --- | --- |
| **Cluster summary** | Основная информация о кластере Storm. |
| **Nimbus summary** | A list of basic Nimbus information. |
| **Topology summary** | Список запущенных топологий. To view more information about a specific topology, select its link in the **Name** column. |
| **Supervisor summary** | Информация о контролере Storm. To see the worker resources associated with a specific supervisor, select its link in the **Host** or **Id** column. |
| **Nimbus configuration** | Конфигурация Nimbus для кластера. |

The Storm UI main page looks similar to this web page:

![Main page, Storm UI, Apache Storm topologies, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Topology summary

При выборе ссылки в разделе **Topology summary** (Сводка по топологиям) отображается следующая информация о топологии.

| Section | Описание |
| --- | --- |
| **Topology summary** | Общие сведения о топологии. |
| **Topology actions** | Management actions that you can do for the topology. The available actions are described later in this section. |
| **Topology stats** | Статистика по топологии. To set the time frame for an entry in this section, select its link in the **Window** column. |
| **Spouts** *(time frame)* | Используемые в топологии источники данных, которые называются воронками. To view more information about a specific spout, select its link in the **Id** column. |
| **Bolts** *(time frame)* | Используемые в топологии обработчики данных, которые называются ситами. To view more information about a specific bolt, select its link in the **Id** column. |
| **Worker resources** | A list of worker resources. To view more information about a specific worker resource, select its link in the **Host** column. |
| **Topology visualization** | A **Show Visualization** button that displays a visualization of the topology. |
| **Topology configuration** | Конфигурация выбранной топологии. |

The Storm topology summary page looks similar to this web page:

![Topology summary page, Storm UI, Apache Storm, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

In the **Topology actions** section, you can select the following buttons to do an action:

| Кнопка | Описание |
| --- | --- |
| **Активировать** | Возобновление обработки отключенной топологии. |
| **Deactivate** | Приостановка выполняемой топологии. |
| **Rebalance** | Корректировка параллелизма топологии. You should rebalance running topologies after you've changed the number of nodes in the cluster. This operation allows the topology to adjust parallelism to compensate for the additional or reduced number of nodes in the cluster.<br/><br/>Дополнительные сведения см. в статье о <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">параллелизме топологии Apache Storm</a>.
| **Kill** | Останавливает выполнение топологии Storm по истечении заданного времени ожидания. |
| **Отладка** | Begins a debugging session for the running topology. |
| **Stop Debug** | Ends the debugging session for the running topology. |
| **Change Log Level** | Modifies the debugging log level. |

##### <a name="spout-and-bolt-summary"></a>Spout and bolt summary

При выборе воронки в разделе **Spouts** (Воронки) или **Bolts** (Сита) отображается следующая информация о выбранном элементе.

| Section | Описание |
| --- | --- |
| **Component summary** | Основная информация о воронке или сите. |
| **Component actions** | **Debug** and **Stop Debug** buttons. |
| **Spout stats** or **Bolt stats** | Статистика по воронке или ситу. To set the time frame for an entry in this section, select its link in the **Window** column. |
| (Bolt-only)<br/>**Input stats** *(time frame)* | Информация о входных потоках, проходящих через сито. |
| **Output stats** *(time frame)* | Информация о потоках, создаваемых компонентами воронки или сита. |
| **Profiling and debugging** | Controls for profiling and debugging the components on this page. You can set the **Status / Timeout (Minutes)** value, and you can select buttons for **JStack**, **Restart Worker**, and **Heap**. |
| **Executors** *(time frame)* | Информация об экземплярах воронки или сита. To view a log of diagnostic information produced for this instance, select the **Port** entry for a specific executor. You can also see the worker resources associated with a specific executor by selecting its link in the **Host** column. |
| **Ошибки** | Информация об ошибках для компонентов воронки или сита. |

The Storm bolt summary page looks similar to this web page:

![Bolt summary page, Storm UI, Apache Storm, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Monitor and manage the topology using the REST API

The Storm UI is built on top of the REST API, so you can do similar management and monitoring tasks by using the REST API. С помощью REST API можно создать пользовательские средства для отслеживания топологий Storm и управления ими.

Дополнительные сведения см. в статье о [REST API пользовательского интерфейса Apache Storm](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Следующая информация касается использования REST API с Apache Storm в HDInsight.

> [!IMPORTANT]  
> The Storm REST API is not publicly available over the internet. It must be accessed using an SSH tunnel to the HDInsight cluster head node. For information on creating and using an SSH tunnel, see [Use SSH tunneling to access Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Базовый универсальный код ресурса

The base URI for the REST API on Linux-based HDInsight clusters is available at URL address `https://HEADNODEFQDN:8744/api/v1/`, where you replace *HEADNODEFQDN* with the head node. The domain name of the head node is generated during cluster creation and isn't static.

You can find the fully qualified domain name (FQDN) for the cluster head node in several ways:

| FQDN discovery method | Описание |
| --- | --- |
| SSH session | Use the command `headnode -f` from an SSH session to the cluster. |
| Ambari Web | On the Ambari cluster web page (`https://CLUSTERNAME.azurehdinsight.net`), select **Services** from the top of the page, then select **Storm**. На вкладке **Summary** (Сводка) выберите **Storm UI Server** (Сервер пользовательского интерфейса Storm). Полное доменное имя узла, на котором размещены пользовательский интерфейс Storm и REST API, отображено в верхней части страницы. |
| Ambari REST API | Use the command `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` to retrieve information about the node that the Storm UI and REST API are running on. Replace the two instances of *CLUSTERNAME* with the cluster name. When you're prompted, enter the password for the user (admin) account. In the response, the "host_name" entry of the JSON output contains the FQDN of the node. |

### <a name="authentication"></a>Authentication

Requests to the REST API must use *basic authentication*, so you have to use the administrator name and password for the HDInsight cluster.

> [!NOTE]  
> Так как при обычной проверке подлинности данные отправляются открытым текстом, следует *всегда* использовать HTTPS для защиты связи с кластером.

### <a name="return-values"></a>Возвращаемые значения

Сведения, возвращаемые из REST API, могут использоваться только в пределах кластера. For example, the fully qualified domain name (FQDN) returned for [Apache ZooKeeper](https://zookeeper.apache.org/) servers isn't accessible from the internet.

## <a name="next-steps"></a>Дальнейшие действия

См. дополнительные сведения о [разработке топологий на основе Java с помощью Apache Maven](apache-storm-develop-java-topology.md).

For a list of more example topologies, see [Example Apache Storm topologies in Azure HDInsight](apache-storm-example-topology.md).
