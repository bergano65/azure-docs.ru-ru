---
title: Установка и использование Giraph для кластеров Hadoop в HDInsight в Azure
description: Дополнительные сведения о настройке кластера HDInsight с Giraph и использование Giraph.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 4bcd7bcef0969cec82d42ead6307e72a3abea6e1
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978874"
---
# <a name="install-and-use-apache-giraph-on-windows-based-hdinsight-clusters"></a>Установка и использование Apache Giraph в кластерах HDInsight под управлением Windows

Узнайте, как настраивать кластер HDInsight на основе Windows с Giraph с помощью действия скрипта и использовать Giraph для обработки крупных графов. См. дополнительные сведения об [использовании Apache Giraph в кластерах HDInsight Hadoop под управлением Linux](hdinsight-hadoop-giraph-install-linux.md).

> [!IMPORTANT]  
> Шаги, описанные в этом документе, можно применять только к кластерам HDInsight под управлением Windows. Для версий ниже HDInsight 3.4 кластер HDInsight доступен только в Windows. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement). См. дополнительные сведения об [установке Apache Giraph в кластерах HDInsight Hadoop под управлением Linux](hdinsight-hadoop-giraph-install-linux.md).


Giraph можно установить в кластере любого типа (Hadoop, Storm, HBase, Spark) в HDInsight в Azure, воспользовавшись *действием скрипта*. Пример сценария для установки Giraph в кластере HDInsight доступен в большом двоичном объекте службы хранилища Azure (доступ только для чтения) по адресу [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). Пример скрипта работает только с кластером HDInsight версии 3.1. Дополнительную информацию о версиях кластера HDInsight см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight](hdinsight-component-versioning.md).

**Связанные статьи**

* [Установка Apache Giraph в кластерах HDInsight Hadoop под управлением Linux](hdinsight-hadoop-giraph-install-linux.md)
* [Создание кластеров Apache Hadoop под управлением Windows в HDInsight](hdinsight-provision-clusters.md) — общие сведения о создании кластеров HDInsight.
* [Настройка кластера HDInsight с помощью действия сценария][hdinsight-cluster-customize]. Общая информация о настройке кластеров HDInsight с помощью действия сценария.
* [Разработка скриптов действия сценария для HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>Что такое Giraph?
<a href="https://giraph.apache.org/" target="_blank">Apache Giraph</a> позволяет выполнять обработку графов с использованием Hadoop и может использоваться с Azure HDInsight. Графы моделируют взаимоотношения между объектами, такие как подключения между маршрутизаторами в большой сети, подобной Интернету, или взаимоотношения между людьми в социальных сетях (иногда называется социальным графом). Обработка графов дает возможность делать выводы о взаимоотношениях объектов в графе, таких как:

* определение потенциальных друзей на основе текущих взаимоотношений;
* определение кратчайшего маршрута между двумя компьютерами в сети;
* вычисление ранга страницы для веб-страниц.

## <a name="install-giraph-using-portal"></a>Установка Giraph с помощью портала
1. Начните создание кластера с помощью параметра **Настраиваемое создание**, как описано в статье [Создание кластеров Hadoop под управлением Windows в HDInsight](hdinsight-provision-clusters.md).
2. На странице **Действия скрипта** мастера щелкните **Добавить действие скрипта** для предоставления сведений о данном действии скрипта, как показано ниже.

    ![Использование действия сценария для настройки кластера](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Использование действия сценария для настройки кластера")

    |Свойство|Значение|  
    |---|---|  
    |ИМЯ|Укажите имя для действия сценария. Например **Install Giraph**.|
    |URI-адрес сценария|Укажите универсальный идентификатор ресурса (URI) для сценария, который вызывается для настройки кластера. Например, *https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1*.|
    |Тип узла|Укажите узлы, на которых выполняется сценарий настройки. Вы можете выбрать одно из трех значений: **Все узлы**, **Только головные узлы** или **Worker nodes only** (Только рабочие узлы).
    |Параметры|Укажите параметры, если они требуются для сценария. Скрипт для установки Giraph не требует никаких параметров, поэтому можно оставить это поле пустым.|  

    Можно добавить несколько действий сценария для установки нескольких компонентов в кластере. После добавления скриптов щелкните флажок, чтобы начать создание кластера.

## <a name="use-giraph"></a>Использование Giraph
Мы используем пример SimpleShortestPathsComputation, который демонстрирует базовую реализацию <a href = "https://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> для поиска кратчайшего пути между объектами графа. Выполните следующие действия, чтобы отправить данные и JAR-файл примера, запустить задание с использованием примера SimpleShortestPathsComputation, а затем просмотреть результаты.

1. Передайте образец файла данных в хранилище BLOB-объектов Azure. На локальной рабочей станции создайте новый файл с именем **tiny_graph.txt**. В нем должны присутствовать следующие строки.

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Отправьте файл tiny_graph.txt в основное хранилище для кластера HDInsight. См. дополнительные сведения об [отправке данных для заданий Apache Hadoop в HDInsight](hdinsight-upload-data.md).

    Эти данные описывают взаимоотношения между объектами в направленном графе с использованием формата [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Каждая строка представляет взаимоотношение между объектом **source\_id** и одним или несколькими объектами **dest\_id**. Значение **edge\_value** (или вес) можно представить себе как силу или расстояние подключения между **source_id** и **dest\_id**.

    В визуальной форме и с использованием значения (или веса) в качестве расстояния между объектами указанные выше данные могут выглядеть следующим образом.

    ![tiny_graph.txt начерчен в виде кругов с линиями различной длины между](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)

2. Запустите пример SimpleShortestPathsComputation. Воспользуйтесь следующими командлетами Azure PowerShell для запуска этого примера, используя файл tiny_graph.txt в качестве входных данных.

    > [!IMPORTANT]  
    > Поддержка Azure PowerShell для управления ресурсами HDInsight с помощью диспетчера служб Azure (ASM) объявлена **устаревшей** и будет прекращена с 1 января 2017 г. В описанных в этом документе инструкциях используются новые командлеты HDInsight, которые работают с Azure Resource Manager.
    >
    > Чтобы установить последнюю версию Azure PowerShell, выполните действия из статьи [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs). Если у вас есть сценарии, в которые нужно добавить новые командлеты, работающие с Azure Resource Manager, см. статью [Переход к средствам разработки на основе Azure Resource Manager для кластеров HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

    ```powershell
    $clusterName = "clustername"
    # Giraph examples jar
    $jarFile = "wasb:///example/jars/giraph-examples.jar"
    # Arguments for this job
    $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
                    "-ca", "mapred.job.tracker=headnodehost:9010",
                    "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
                    "-vip", "wasb:///example/data/tiny_graph.txt",
                    "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
                    "-op",  "wasb:///example/output/shortestpaths",
                    "-w", "2"
    # Create the definition
    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition
        -JarFile $jarFile
        -ClassName "org.apache.giraph.GiraphRunner"
        -Arguments $jobArguments

    # Run the job, write output to the Azure PowerShell window
    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job
    Write-Host "STDERR"
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
    ```

    В приведенном выше примере замените **clustername** именем кластера HDInsight, где установлен Giraph.
3. Просмотрите результаты. Когда задание будет завершено, результаты будут храниться в двух выходных файлах в папке **wasb:///example/out/shotestpaths**. Эти файлы называются **part-m-00001** и **part-m-00002**. Выполните следующие действия, чтобы скачать и просмотреть выходные данные.

    ```powershell
    $subscriptionName = "<SubscriptionName>"       # Azure subscription name
    $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
    $containerName = "<ContainerName>"             # Blob storage container name

    # Select the current subscription
    Select-AzureSubscription $subscriptionName

    # Create the Storage account context object
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Download the job output to the workstation
    Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
    Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force
    ```

    В результате создается структура каталога **example/output/shortestpaths** в текущем каталоге на рабочей станции, а выходные файлы скачиваются в это расположение.

    Используйте командлет **Cat**, чтобы отобразить содержимое файлов.

        Cat example/output/shortestpaths/part*

    Результат должен выглядеть аналогично следующему:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    Пример SimpleShortestPathComputation жестко запрограммирован для запуска с объекта ID 1 и поиска кратчайшего пути к другим объектам. Таким образом, выходные данные должны считываться как `destination_id distance`, где расстояние представляет собой значение (или вес) переходов на пути между объектом ID 1 и целевым объектом ID.

    При визуализации можно проверить результаты, проходя кратчайшие пути между ID 1 и всеми другими объектами. Обратите внимание, что кратчайший путь между ID 1 и ID 4 — это 5. Это общее расстояние между объектами <span style="color:orange">ID 1 и ID 3</span> и между объектами <span style="color:red">ID 3 и ID 4</span>.

    ![Представление объектов в виде кругов с кратчайшими путями между ними](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-azure-powershell"></a>Установка Giraph с помощью Azure PowerShell
См. статью [Настройка кластеров HDInsight под управлением Linux с помощью действий сценариев](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  В этом примере показано, как установить Apache Spark с помощью Azure PowerShell. Нужно настроить скрипт для использования [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Установка Giraph с помощью пакета SDK для .NET
См. статью [Настройка кластеров HDInsight под управлением Linux с помощью действий сценариев](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). В этом примере показано, как установить Spark с помощью пакета SDK для .NET. Нужно настроить скрипт для использования [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="see-also"></a>См. также
* [Установка Apache Giraph в кластерах HDInsight Hadoop под управлением Linux](hdinsight-hadoop-giraph-install-linux.md)
* [Создание кластеров Apache Hadoop под управлением Windows в HDInsight](hdinsight-provision-clusters.md) — общие сведения о создании кластеров HDInsight.
* [Настройка кластера HDInsight с помощью действия сценария][hdinsight-cluster-customize]. Общая информация о настройке кластеров HDInsight с помощью действия сценария.
* [Разработка скриптов действия сценария для HDInsight](hdinsight-hadoop-script-actions.md).
* [Краткое руководство. Создание кластера Apache Spark в HDInsight с помощью шаблона][hdinsight-install-spark]. Пример скрипта действия для установки Spark.
* [Установка и использование Apache Solr в кластерах HDInsight](hdinsight-hadoop-solr-install.md). Пример скрипта действия для установки Solr.

[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
