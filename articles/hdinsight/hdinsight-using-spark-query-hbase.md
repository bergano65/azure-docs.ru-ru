---
title: Чтение и запись данных HBase с помощью Spark в Azure HDInsight
description: Чтение и запись данных из кластера Spark в кластер HBase с помощью соединителя Spark HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: fdfd026be1a10410cd7c875dbdf0de9660c8412c
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937620"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Чтение и запись данных Apache HBase с помощью Apache Spark

Обычно для запроса Apache HBase применяется низкоуровневый API (сканирует, получает и помещает) или синтаксис SQL, использующий Apache Phoenix. Apache также предоставляет соединитель Apache Spark HBase, который является удобной и эффективной альтернативой запросу и изменению данных, хранимых в HBase.

## <a name="prerequisites"></a>Предварительные требования

* Два отдельных кластера HDInsight развернуты в одной виртуальной сети. Одна HBase и одна Spark с установленным как минимум Spark 2,1 (HDInsight 3,6). Дополнительные сведения см. в статье [Создание кластеров под управлением Linux в HDInsight с помощью портала Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

* Клиент SSH. Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* [Схема универсального кода ресурса (URI)](hdinsight-hadoop-linux-information.md#URI-and-scheme) для основного хранилища кластеров. Это будет wasb://для хранилища BLOB-объектов Azure, abfs://для Azure Data Lake Storage 2-го поколения или adl://для Azure Data Lake Storage 1-го поколения. Если для хранилища BLOB-объектов включено безопасное перемещение, URI будет иметь `wasbs://`значение.  См. также сведения о [безопасной передаче](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Общий процесс

Ниже приведен общий процесс, чтобы позволить кластеру Spark запрашивать кластер HDInsight.

1. Подготовка демонстрационных данных в HBase.
2. Извлечение файла hbase-site.xml из папки конфигурации кластера HBase (/etc/hbase/conf).
3. Помещение копии файла hbase-site.xml в папку конфигурации Spark 2 (/etc/spark2/conf).
4. Запуск `spark-shell` со ссылкой на соединитель Spark HBase по его координатам Maven в параметре `packages`.
5. Определение каталога, который сопоставляет схему из Spark с HBase.
6. Взаимодействие с данными HBase с помощью API RDD или таблицы данных.

## <a name="prepare-sample-data-in-apache-hbase"></a>Подготовка демонстрационных данных в Apache HBase

На этом шаге вы создадите и заполните таблицу в Apache HBase, которую затем можно будет запрашивать с помощью Spark.

1. `ssh` Используйте команду для подключения к кластеру HBase. Измените приведенную ниже команду, `HBASECLUSTER` заменив именем кластера HBase, а затем введите следующую команду:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. `hbase shell` Используйте команду, чтобы запустить интерактивную оболочку HBase. В строку SSH-подключения введите следующую команду:

    ```bash
    hbase shell
    ```

3. Используйте команду `create` , чтобы создать таблицу HBase с семейством из двух столбцов. Введите следующую команду:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. `put` Используйте команду, чтобы вставить значения из указанного столбца в указанную строку в определенной таблице. Введите следующую команду:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. `exit` Используйте команду, чтобы прерывать интерактивную оболочку HBase. Введите следующую команду:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Копирование файл HBase-site. XML в кластер Spark

Скопируйте файл файл HBase-site. XML из локального хранилища в корневую папку хранилища по умолчанию кластера Spark.  Измените приведенную ниже команду, чтобы отразить конфигурацию.  Затем из открытого сеанса SSH в кластер HBase введите команду:

| Значение синтаксиса | Новое значение|
|---|---|
|[Схема URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Измените в соответствии с хранилищем.  Приведенный ниже синтаксис предназначен для хранилища BLOB-объектов с включенной безопасной переносю.|
|`SPARK_STORAGE_CONTAINER`|Замените на имя контейнера хранилища по умолчанию, используемое для кластера Spark.|
|`SPARK_STORAGE_ACCOUNT`|Замените именем учетной записи хранения по умолчанию, используемой для кластера Spark.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Затем завершите подключение SSH к кластеру HBase.

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Помещение файла hbase-site.xml в кластер Spark

1. Подключитесь к головному узлу кластера Spark с помощью SSH.

2. Введите следующую команду, чтобы скопировать `hbase-site.xml` данные из хранилища по умолчанию кластера Spark в папку конфигурации Spark 2 в локальном хранилище кластера:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Запуск оболочки Shell со ссылкой на соединитель Spark HBase

1. В открытом сеансе SSH в кластере Spark введите следующую команду, чтобы запустить оболочку Spark:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Оставьте открытым экземпляр оболочки Spark и перейдите к следующему шагу.

## <a name="define-a-catalog-and-query"></a>Определение каталога и отправка запроса

На этом этапе вы определяете объект каталога, который сопоставляет схему из Spark с Apache HBase.  

1. В открытой оболочке Spark введите следующие `import` инструкции:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

2. Введите следующую команду, чтобы определить каталог для таблицы Contacts, созданной в HBase:

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    Код делает следующее:  

     1\. Определите схему каталога для таблицы HBase с именем `Contacts`.  
     2\. Определите rowkey как `key` и сопоставьте имена столбцов, используемые в Spark, с семейством столбцов, именем столбца и типом столбца, используемыми в HBase.  
     В. rowkey также должен быть определен как именованный столбец (`rowkey`), который содержит определенное семейство столбцов `cf` из `rowkey`.  

3. Введите следующую команду, чтобы определить метод, который предоставляет кадр `Contacts` данных для таблицы в HBase:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

4. Создайте экземпляр таблицы данных:

    ```scala
    val df = withCatalog(catalog)
    ```  

5. Выполните запрос таблицы данных:

    ```scala
    df.show()
    ```

6. Вы должны увидеть две строки данных:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Зарегистрируйте временную таблицу, чтобы запрашивать таблицу HBase с помощью Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

8. Выполните SQL-запрос к таблице `contacts`:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

9. Вы должны увидеть примерно такой результат:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Вставка новых данных

1. Чтобы вставить новую запись о контакте, определите класс `ContactRecord`:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

2. Создайте экземпляр `ContactRecord` и поместите его в массив:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

3. Сохраните массив новых данных в HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

4. Изучите результаты:

    ```scala  
    df.show()
    ```

5. Вы должны увидеть примерно такой результат:

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

6. Закройте оболочку Spark, введя следующую команду:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Следующие шаги

* [Соединитель Apache Spark HBase](https://github.com/hortonworks-spark/shc)
