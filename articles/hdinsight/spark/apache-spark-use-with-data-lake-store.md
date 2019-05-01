---
title: Использование Apache Spark для анализа данных в Azure Data Lake Storage 1-го поколения
description: Выполнение заданий Spark для анализа хранимых данных в Azure Data Lake Storage 1-го поколения
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.openlocfilehash: 688b87fcc0ec18e0bf5924470d521c44229ae421
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682889"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>Использование кластера HDInsight Spark для анализа данных в Data Lake Storage 1-го поколения

В этом руководстве для выполнения задания, которое считывает данные из учетной записи Data Lake Storage, используется [Jupyter Notebook](https://jupyter.org/) с кластерами HDInsight Spark.

## <a name="prerequisites"></a>Технические условия

* Учетная запись Azure Data Lake Storage 1-го поколения. Следуйте инструкциям из статьи [Начало работы с Azure Data Lake Storage Gen1 с помощью портала Azure](../../data-lake-store/data-lake-store-get-started-portal.md).

* Кластер Azure HDInsight Spark с Data Lake Storage 1-го поколения в качестве хранилища. Следуйте указаниям, приведенным здесь — [Краткое руководство. по настройке кластеров в HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    
## <a name="prepare-the-data"></a>Подготовка данных

> [!NOTE]  
> Этот шаг не нужно выполнять, если вы создали кластер HDInsight, использующий Data Lake Storage в качестве хранилища по умолчанию. Процесс создания кластера добавляет демонстрационные данные в учетную запись Data Lake Storage, указанную при создании кластера. Перейдите к разделу "Использование кластера HDInsight Spark с Data Lake Storage".

Если вы создали кластер HDInsight, использующий Data Lake Storage в качестве дополнительного хранилища и хранилище BLOB-объектов Azure как хранилище по умолчанию, то в учетную запись Data Lake Storage сначала следует скопировать демонстрационные данные. Вы можете использовать пример данных из хранилища BLOB-объектов Azure, связанного с кластером HDInsight. Для этого можно использовать [инструмент ADLCopy](https://aka.ms/downloadadlcopy). Скачайте и установите этот инструмент с помощью следующей ссылки.

1. Откройте командную строку и перейдите в каталог, в который установлено средство AdlCopy, обычно `%HOMEPATH%\Documents\adlcopy`.

2. Выполните следующую команду, чтобы скопировать заданный большой двоичный объект из контейнера-источника в Data Lake Storage.

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Скопируйте пример файла данных **HVAC.csv**, расположенный в папке **/HdiSamples/HdiSamples/SensorSampleData/hvac/**, в учетную запись Azure Data Lake Storage. Фрагмент кода должен иметь следующий вид.

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > Убедитесь, что имена файла и пути используют правильный регистр.

3. Вам будет предложено ввести учетные данные для подписки Azure, в которой расположена учетная запись Data Lake Storage. Должен отобразиться результат, аналогичный приведенному ниже фрагменту кода.

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Файл данных (**HVAC.csv**) будет скопирован в папку **/hvac** в учетной записи Data Lake Storage.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Использование кластера HDInsight Spark с Data Lake Storage 1-го поколения

1. На начальной панели [портала Azure](https://portal.azure.com/) щелкните плитку кластера Apache Spark (если она закреплена на начальной панели). Кроме того, вы можете перейти к кластеру, последовательно щелкнув **Просмотреть все** > **Кластеры HDInsight**.

2. В колонке кластера Spark щелкните **Быстрые ссылки**, затем в колонке **Панель мониторинга кластера** выберите **Записная книжка Jupyter**. При появлении запроса введите учетные данные администратора для кластера.

   > [!NOTE]  
   > Также можно открыть Jupyter Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените **CLUSTERNAME** именем кластера:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Создайте новую записную книжку. Щелкните **Создать**, а затем выберите **PySpark**.

    ![Создание записной книжки Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Создание записной книжки Jupyter")

4. Так как записная книжка была создана с помощью ядра PySpark, задавать контексты явно необязательно. Контексты Spark и Hive будут созданы автоматически при выполнении первой ячейки кода. Можно начать с импорта различных типов, необходимых для этого сценария. Для этого вставьте следующий фрагмент кода в ячейку и нажмите сочетание клавиш **SHIFT+ВВОД**.

        from pyspark.sql.types import *

    При каждом запуске задания в Jupyter в заголовке окна веб-браузера будет отображаться состояние **(Занято)**, а также название записной книжки. Кроме того, рядом с надписью **PySpark** в верхнем правом углу окна будет показан закрашенный кружок. После завершения задания этот значок изменится на кружок без заливки.

     ![Состояние задания записной книжки Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Состояние задания записной книжки Jupyter")

5. Загрузите пример данных во временную таблицу с помощью файла **HVAC.csv**, скопированного в учетную запись Data Lake Storage 1-го поколения. Получить доступ к данным в учетной записи Data Lake Storage можно с помощью следующего шаблона URL-адреса.

   * Если Data Lake Storage 1-го поколения используется в качестве хранилища по умолчанию, путь к файлу HVAC.csv будет похож на следующий URL-адрес.

           adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

       Можно также использовать сокращенный формат, как показано ниже.

           adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

   * Если Data Lake Storage используется в качестве дополнительного хранилища, файл HVAC.csv будет находиться в расположении, куда он был скопирован. Например:

           adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     вставьте приведенный ниже код в пустую ячейку, замените **MYDATALAKESTORE** именем учетной записи Data Lake Storage и нажмите клавиши **SHIFT + ВВОД**. Этот пример кода регистрирует данные во временной таблице с именем **hvac**.

           # Load the data. The path below assumes Data Lake Storage is default storage for the Spark cluster
           hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

           # Create the schema
           hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

           # Parse the data in hvacText
           hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

           # Create a data frame
           hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

           # Register the data fram as a table to run queries against
           hvacdf.registerTempTable("hvac")

6. Так как вы используете ядро PySpark, вы можете отправить SQL-запрос непосредственно к временной таблице **hvac**, которую вы только что создали с помощью магической команды `%%sql`. Дополнительные сведения о команде magic `%%sql` и о других командах magic, доступных в ядре PySpark, приведены в статье [Ядра для записной книжки Jupyter в кластерах Apache Spark в Azure HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. После успешного выполнения задания по умолчанию будет показаны следующие табличные данные.

      ![Вывод результатов запроса в виде таблицы](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Вывод результатов запроса в виде таблицы")

     Результаты также можно просмотреть и в других визуализациях. Например, диаграмма областей для тех же выходных данных будет выглядеть следующим образом.

     ![Диаграмма с областями, показывающая результат запроса](./media/apache-spark-use-with-data-lake-store/jupyter-area-output.png "Диаграмма с областями, показывающая результат запроса")

8. Завершив работу с приложением, следует закрыть записную книжку, чтобы освободить ресурсы. Для этого в записной книжке в меню **Файл** выберите пункт **Close and Halt** (Закрыть и остановить). Это завершит работу записной книжки и закроет ее.


## <a name="next-steps"></a>Дальнейшие действия

* [Создание автономного приложения Scala для работы в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-create-standalone-application.md)
* [Создание приложений Apache Spark для кластера HDInsight с помощью набора средств Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Создание приложений Apache Spark для кластера HDInsight с помощью Azure Toolkit for Eclipse](apache-spark-eclipse-tool-plugin.md)
* [Использование Azure Data Lake Storage Gen2 с кластерами Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
