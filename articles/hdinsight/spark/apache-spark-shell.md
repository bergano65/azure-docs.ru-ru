---
title: Использование интерактивной оболочки Spark в Azure HDInsight
description: Интерактивная оболочка Spark предоставляет процесс чтения, выполнения и вывода для поочередного выполнения команд Spark и просмотра результатов.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/12/2019
ms.openlocfilehash: f088b8210b8170d22e84d131f0a72f5f8caa3b92
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435228"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Запуск Apache Spark из оболочки Spark

Интерактивная оболочка [Apache Spark](https://spark.apache.org/) предоставляет среду REPL (цикл чтения, выполнения и вывода) для поочередного выполнения команд Spark и просмотра результатов. Этот процесс применяется при развертывании и отладке. Spark предоставляет оболочки для каждого из поддерживаемых языков: Scala, Python и R.

## <a name="run-an-apache-spark-shell"></a>Запуск оболочки Apache Spark

1. Используйте [команду SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) для подключения к кластеру. Измените приведенную ниже команду, заменив ИМЯ_КЛАСТЕРА именем кластера, а затем введите следующую команду:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spark предоставляет оболочки для Scala (Spark-Shell) и Python (pyspark). В сеансе SSH введите одну из следующих команд:

    ```bash
    spark-shell
    pyspark
    ```

    Теперь вы можете вводить команды Spark на соответствующем языке.

1. Ниже приведено несколько основных команд:

    ```scala
    // Load data
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")

    // Show data
    data.show()

    // Select certain columns
    data.select($"BuildingID", $"Country").show(10)

    // exit shell
    :q
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>Экземпляры SparkSession и SparkContext

По умолчанию экземпляры SparkSession и SparkContext создаются автоматически при запуске оболочки Spark.

Чтобы получить доступ к экземпляру SparkSession, введите команду `spark`. А чтобы получить доступ к экземпляру SparkContext, введите команду `sc`.

## <a name="important-shell-parameters"></a>Важные параметры оболочки

Команда оболочки Spark (`spark-shell`или `pyspark`) поддерживает множество параметров командной строки. Чтобы просмотреть полный список параметров, запустите оболочку Spark с параметром `--help`. Некоторые из этих параметров могут применяться только к `spark-submit`, для которых оболочка Spark заносится в оболочку.

| Параметр | description | пример |
| --- | --- | --- |
| --master MASTER_URL | Позволяет задать основной URL-адрес. В HDInsight всегда используется значение `yarn`. | `--master yarn`|
| --jars JAR_LIST | Список разделенных запятыми локальных JAR-файлов, которые добавляются в пути к классам драйвера и исполнителя. В HDInsight этот список состоит из путей к файловой системе по умолчанию в службе хранилища Azure или в Data Lake Storage. | `--jars /path/to/examples.jar` |
| --packages MAVEN_COORDS | Список разделенных запятыми координат Maven для JAR-файлов, которые добавляются в пути к классам драйвера и исполнителя. Выполняет поиск локального репозитория Maven, затем центрального репозитория Maven или же любого дополнительного удаленного репозитория, заданного с помощью параметра `--repositories`. Формат координат: *groupId*:*artifactId*:*version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LIST | (Только для Python.) Список разделенных запятыми ZIP-, EGG- или PY-файлов, которые размещаются в PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Дальнейшие действия

- Вводную информацию см. в статье [Общие сведения об Apache Spark в Azure HDInsight](apache-spark-overview.md).
- Сведения о работе со SparkSQL и кластерами Spark см. в статье [Создание кластера Apache Spark в Azure HDInsight](apache-spark-jupyter-spark-sql.md).
- Сведения о создании приложений для обработки потоковых данных с помощью Spark см. в статье [Общие сведения о потоковой передаче Apache Spark](apache-spark-streaming-overview.md).
