---
title: Использование интерактивной оболочки Spark в Azure HDInsight
description: Интерактивная оболочка Spark предоставляет процесс чтения, выполнения и вывода для поочередного выполнения команд Spark и просмотра результатов.
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: 9044ed3ad9cf9ffa2f54d130bb50b37df121b86f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116079"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Запуск Apache Spark из оболочки Spark

Интерактивная оболочка [Apache Spark](https://spark.apache.org/) предоставляет среду REPL (цикл чтения, выполнения и вывода) для поочередного выполнения команд Spark и просмотра результатов. Этот процесс применяется при развертывании и отладке. Spark предоставляет оболочки для каждого из поддерживаемых языков: Scala, Python и R.

## <a name="get-to-an-apache-spark-shell-with-ssh"></a>Доступ к оболочке Apache Spark по протоколу SSH

Получите доступ к оболочке Apache Spark в HDInsight, подключившись к первичному головному узлу кластера по протоколу SSH:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Вы можете скопировать полную команду SSH для кластера на портале Azure:

1. Войдите на [портал Azure](https://portal.azure.com).
2. Перейдите в область своего кластера HDInsight Spark.
3. Выберите Secure Shell (SSH).

    ![Область HDInsight на портале Azure](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Скопируйте отображаемую команду SSH и выполните ее в окне терминала.

    ![Область HDInsight SSH на портале Azure](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Дополнительные сведения об использовании SSH для подключения к HDInsight см. в разделе [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-an-apache-spark-shell"></a>Запуск оболочки Apache Spark

Spark предоставляет оболочки для Scala (spark-shell), Python (pyspark) и R (sparkR). В сеансе SSH на головном узле кластера HDInsight введите одну из следующих команд:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Теперь вы можете вводить команды Spark на соответствующем языке.

## <a name="sparksession-and-sparkcontext-instances"></a>Экземпляры SparkSession и SparkContext

По умолчанию экземпляры SparkSession и SparkContext создаются автоматически при запуске оболочки Spark.

Чтобы получить доступ к экземпляру SparkSession, введите команду `spark`. А чтобы получить доступ к экземпляру SparkContext, введите команду `sc`.

## <a name="important-shell-parameters"></a>Важные параметры оболочки

Команда оболочки Spark (`spark-shell`, `pyspark`, или `sparkR`) поддерживает многие параметры командной строки. Чтобы просмотреть полный список параметров, запустите оболочку Spark с параметром `--help`. Обратите внимание, что некоторые из этих параметров могут применяться только к скрипту `spark-submit`, для которого создается оболочка Spark.

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
