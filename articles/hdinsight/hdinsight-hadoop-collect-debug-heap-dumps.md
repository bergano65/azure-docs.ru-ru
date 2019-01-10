---
title: Отладка и анализ служб Apache Hadoop с помощью дампов кучи в Azure
description: Автоматически собирайте дампы кучи для служб Apache Hadoop и размещайте их в учетной записи хранилища BLOB-объектов Azure для отладки и анализа.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 698806bdedd9994f2c9de53118cb42c9df1c36cd
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724408"
---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-apache-hadoop-services"></a>Сбор дампов кучи в хранилище BLOB-объектов для отладки и анализа служб Apache Hadoop
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Дампы кучи содержат снимок памяти приложения, включая значения переменных на момент создания дампа. Поэтому они полезны для диагностики проблем, возникающих во время выполнения. Сборка дампов кучи для служб [Apache Hadoop](https://hadoop.apache.org/) может выполняться автоматически, после чего они помещаются в учетную запись хранилища BLOB-объектов Azure в раздел HDInsightHeapDumps/.

Сбор дампов кучи для различных служб нужно включать на отдельных кластерах. По умолчанию эта функция отключена. Размер дампов кучи может быть большим, поэтому мы советуем следить за учетной записью хранилища больших двоичных объектов после включения сбора.

> [!IMPORTANT]  
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement). Сведения в этой статье применимы только к HDInsight, работающей под управлением Windows. Сведения о HDInsight под управлением Linux см. в статье [Включение дампов кучи для служб Apache Hadoop в HDInsight под управлением Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md).


## <a name="eligible-services-for-heap-dumps"></a>Службы с возможностью включения дампов кучи
Вы можете включить дампы кучи для следующих служб:

* **Apache Hcatalog** — tempelton;
* **Apache Hive** — hiveserver2, metastore, derbyserver;
* **Mapreduce** — jobhistoryserver;
* **Apache Yarn** — resourcemanager, nodemanager, timelineserver;
* **Apache HDFS** — datanode, secondarynamenode, namenode.

## <a name="configuration-elements-that-enable-heap-dumps"></a>Элементы конфигурации, активирующие дампы кучи
Чтобы включить дампы кучи для службы, необходимо задать необходимые элементы конфигурации в разделе для этой службы, обозначенном аргументом **service_name**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

Значением **service_name** может быть любая из следующих служб: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode или namenode.

## <a name="enable-using-azure-powershell"></a>Включить при помощи Azure PowerShell
Например, чтобы включить дампы кучи с помощью Azure PowerShell для jobhistoryserver, можно использовать следующий скрипт:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Включить при помощи пакета SDK для .NET
Например, чтобы включить дампы кучи с помощью пакета SDK .NET для Azure HDInsight для jobhistoryserver, можно использовать следующий код:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
