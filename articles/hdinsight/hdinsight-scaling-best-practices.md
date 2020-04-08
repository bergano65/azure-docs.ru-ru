---
title: Масштабирование размеров кластера в Azure HDInsight
description: Масштабируйте кластер Apache Hadoop, чтобы соответствовать вашей рабочей нагрузке в Azure HDInsight
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 0fc067f0be4ac5d2b8fa7db9ad7999efe06625a0
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804509"
---
# <a name="scale-azure-hdinsight-clusters"></a>Масштабируйте кластеры Azure HDInsight

HDInsight обеспечивает эластичность с опциями для масштабирования и масштабирования числа рабочих узлов в кластерах. Эта эластичность позволяет уменьшать кластер в нерабочее время или в выходные дни. И расширить его во время пиковых потребностей бизнеса.

Масштабируйте кластер перед периодической обработкой пакетов, чтобы кластер получил достаточные ресурсы. После завершения обработки и сокращения использования масштабируйте кластер HDInsight до меньшего количества рабочих узлов.

Масштабировать кластер можно вручную, используя один из методов, изложенных ниже. Вы также можете использовать [параметры автоматической шкалы](hdinsight-autoscale-clusters.md) для автоматического масштабирования и вниз в ответ на определенные метрики.

> [!NOTE]  
> Поддерживаются только кластеры HDInsight версии 3.1.3 или более поздней. Если вы не знаете версию кластера, см. страницу «Свойства».

## <a name="utilities-to-scale-clusters"></a>Утилиты для масштабирования кластеров

Корпорация Майкрософт предоставляет следующие утилиты для масштабирования кластеров:

|Служебная программа | Описание|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[`Set-AzHDInsightClusterSize`](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) | [`az hdinsight resize`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Azure Classic CLI](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Портал Azure](https://portal.azure.com)|Откройте панель кластера HDInsight, выберите **размер кластера** в левом меню, затем на панели размера кластера, введите число узлов рабочего и выберите Сохранить.|  

![Вариант кластера кластера портала Azure](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

С помощью любого из этих методов можно увеличивать или уменьшать масштаб кластера HDInsight за считанные минуты.

> [!IMPORTANT]  
> * Классический CLI Azure унижается и должен использоваться только с классической моделью развертывания. Для всех остальных развертываний используйте [Azure CLI.](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)
> * Модуль PowerShell AzureRM унипражен.  Пожалуйста, используйте [модуль Az,](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) когда это возможно.

## <a name="impact-of-scaling-operations"></a>Влияние операций масштабирования

При **добавлении** узлов в запущенный кластер HDInsight (масштабирование вверх) задания не будут затронуты. Во время масштабирования можно безопасно передать новые задания. При сбое операции масштабирования сбой приведет к оставить кластер в функциональном состоянии.

При **удалении** узлов (снизу масштабирования) ожидающие или выполняемые задания сбой при завершении операции масштабирования. Этот сбой связан с перезапуском некоторых служб в процессе масштабирования. Кластер может застрять в безопасном режиме во время ручной операции масштабирования.

Ниже представлены возможности, связанные с изменением количества узлов данных в кластере каждого типа, поддерживаемого в HDInsight:

* Apache Hadoop

    Можно легко увеличить количество рабочих узлов в запущенном кластере Hadoop, не влияя на задания. В ходе выполнения операции можно также отправлять новые задания. Сбои в операции масштабирования грациозно обрабатываются. Кластер всегда остается в функциональном состоянии.

    При уменьшении кластера Hadoop с меньшим количеством узлов данных некоторые службы перезапускаются. Это приведет к сбою всех выполняющихся и ожидающих заданий при завершении операции масштабирования. Однако после завершения операции вы можете повторно отправить задания.

* Apache HBase

    Вы можете легко добавить или удалить узлы в кластер HBase во время его работы. Балансировка региональных серверов выполняется автоматически в течение нескольких минут после завершения операции масштабирования. Тем не менее, вы можете вручную сбалансировать региональные серверы. Войти в головной нод кластера и запустить следующие команды:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Дополнительные сведения об использовании оболочки HBase см. в статье [Начало работы с примером Apache HBase в HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Вы можете легко добавить или удалить узлы данных во время запуска Storm. Однако после успешного завершения операции масштабирования необходимо перебалансировать топологию.

    Повторную балансировку можно выполнить двумя способами:

  * с помощью веб-интерфейса Storm;
  * с помощью программы командной строки.

    Для получения дополнительной [Apache Storm documentation](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)информации см.

    В кластере HDInsight доступен веб-интерфейс Storm.

    ![HDInsight, Storm, масштабирование, перераспределение](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Ниже приведен пример команды CLI для повторной балансировки топологии Storm:

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Как безопасно сократить кластер

### <a name="scale-down-a-cluster-with-running-jobs"></a>Масштабирование кластера с запущенными заданиями

Чтобы избежать сбоя выполняемых заданий во время операции снижения масштаба, вы можете попробовать три вещи:

1. Подождите, пока задания будут завершены, прежде чем масштабировать кластер.
1. Вручную прекратите работу.
1. Повторное представление заданий после завершения операции масштабирования.

Чтобы просмотреть список невыполненных и выполняемых заданий, можно использовать **uI-uI**менеджера ресурсов YARN, следующие следующие действия:

1. На [портале Azure](https://portal.azure.com/)выберите кластер.  Инструкции см. в разделе [Отображение кластеров](./hdinsight-administer-use-portal-linux.md#showClusters). Кластер открывается на новой странице портала.
2. С основного вида перейдите к **кластера приборных панелей** > **Ambari дома**. Введите учетные данные кластера.
3. Из uI Ambari выберите **YARN** в списке услуг в левом меню.  
4. На странице YARN выберите **Быстрые ссылки** и нависните над активным головным узлам, а затем выберите **uI-uI управления ресурсами.**

    ![Apache Ambari быстро связывает uI менеджера ресурсов](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

Вы можете получить прямой доступ `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`к uI-доступу менеджера ресурсов с помощью .

Вы увидите список заданий и их текущее состояние. На скриншоте в настоящее время работает одна работа:

![Приложения для управления ресурсами](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Чтобы вручную завершить работу запущенного приложения, из оболочки SSH выполните команду ниже:

```bash
yarn application -kill <application_id>
```

Пример:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Застрять в безопасном режиме

При уменьшении кластера HDInsight использует интерфейсы управления Apache Ambari для первого вывода из эксплуатации дополнительных узлов. Узлы повторяют свои блоки HDFS на другие онлайн-узлы. После этого HDInsight безопасно масштабирует кластер вниз. HDFS переходит в безопасный режим во время операции масштабирования. HDFS должен выйти после того, как масштабирование будет закончено. Однако в некоторых случаях HDFS застревает в безопасном режиме во время операции масштабирования из-за недовоза блока файлов.

По умолчанию HDFS настроен `dfs.replication` с настройкой 1, которая контролирует количество копий каждого файлового блока. Каждая копия блока файлов хранится на другом узлах кластера.

Когда ожидаемое количество копий блоков недоступно, HDFS вступает в безопасный режим, а Ambari генерирует оповещения. HDFS может ввести безопасный режим для масштабирования. Кластер может застрять в безопасном режиме, если необходимое количество узлов не будет обнаружено для репликации.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Примеры ошибок, когда включен безопасный режим

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Вы можете изучить журналы узла имени в папке `/var/log/hadoop/hdfs/` примерно в то же время, когда было выполнено масштабирование кластера, чтобы проследить, когда был активирован безопасный режим. Файлам журнала присвоено имя `Hadoop-hdfs-namenode-<active-headnode-name>.*`.

Основной причиной было то, что Hive зависит от временных файлов в HDFS во время выполнения запросов. Когда HDFS входит в безопасный режим, Hive не может запускать запросы, потому что он не может писать в HDFS. Файлы Temp в HDFS расположены в локальном приводе, установленном на отдельных рабочих узлах VMs. Файлы реплицируются среди других узлов рабочего с тремя копиями, минимум.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Как предотвратить застрять в безопасном режиме HDInsight

Есть несколько способов предотвратить зависание HDInsight в безопасном режиме.

* Остановите все задания Hive перед уменьшением масштаба HDInsight. Кроме того, можно запланировать операцию уменьшения масштаба, чтобы избежать конфликта с запущенными заданиями Hive.
* Вручную очистите пустые файлы в каталоге `tmp` Hive в HDFS, а затем приступите к уменьшению масштаба.
* Выполните уменьшение масштаба HDInsight как минимум до трех рабочих узлов. Старайтесь избежать масштабирования до одного рабочего узла.
* При необходимости выполните команду, чтобы отключить безопасный режим.

В разделах ниже описываются эти параметры.

#### <a name="stop-all-hive-jobs"></a>Остановка всех заданий Hive

Остановите все задания Hive перед уменьшением масштаба до одного рабочего узла. Если запланирована рабочая нагрузка, выполните уменьшение масштаба после завершения работы Hive.

Остановка заданий Hive перед масштабированием помогает свести к минимуму количество файлов царапин в папке tmp (если таковые имеется).

#### <a name="manually-clean-up-hives-scratch-files"></a>Очистка пустых файлов Hive вручную

Если при работе Hive остались временные файлы, их можно вручную очистить, а затем выполнить уменьшение масштаба, чтобы избежать активации безопасного режима.

1. Проверьте, какое местоположение используется для временных файлов Hive, посмотрев свойство конфигурации. `hive.exec.scratchdir` Этот параметр `/etc/hive/conf/hive-site.xml`устанавливается в пределах:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Остановите службы Hive и убедитесь, что выполнены все задания и запросы.

1. Перечислите содержимое каталога царапин, `hdfs://mycluster/tmp/hive/` найденного выше, чтобы увидеть, содержит ли он какие-либо файлы:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Ниже приведен пример выходных данных при наличии файлов:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

1. Если эти файлы обработаны Hive, их можно удалить. Убедитесь, что у Hive нет запросов, загнав страницу uI Yarn Resource Manager.

    Пример командной строки для удаления файлов из HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Масштабируйте HDInsight до трех или более рабочих узлов

Если кластеры часто застревают в безопасном режиме при сокращении до менее чем трех рабочих узлов, сохраните по крайней мере три рабочих узла.

Наличие трех рабочих узлов обходится дороже, чем масштабирование до одного рабочего узла. Однако это действие предотвратит застрять в безопасном режиме вашего кластера.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Масштабируйте HDInsight до одного рабочего узла

Даже когда кластер масштабируется до одного узла, рабочий узла 0 все равно выживет. Рабочий узлы 0 никогда не могут быть выведены из эксплуатации.

#### <a name="run-the-command-to-leave-safe-mode"></a>Выполнение команды для отключения безопасного режима

Окончательный вариант заключается в выполнении команды безопасного режима отпуска. Если HDFS вошел в безопасный режим из-за недовоспроизводимого файла Hive, выполните следующую команду, чтобы выйти из безопасного режима:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Масштабирование кластера Apache HBase

Серверы региона автоматически уравновешиваются в течение нескольких минут после завершения операции масштабирования. Чтобы вручную сбалансировать серверы регионов, выполните следующие действия:

1. Подключитесь к кластеру HDInsight по протоколу SSH. Для получения дополнительной информации [см.](hdinsight-hadoop-linux-use-ssh-unix.md)

2. Запустите оболочку HBase:

    ```bash
    hbase shell
    ```

3. Используйте команду ниже, чтобы вручную распределить нагрузку между региональными серверами:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Дальнейшие действия

* [Автоматическое масштабирование кластеров Azure HDInsight](hdinsight-autoscale-clusters.md)
* [Введение в Azure HDInsight](hadoop/apache-hadoop-introduction.md)
