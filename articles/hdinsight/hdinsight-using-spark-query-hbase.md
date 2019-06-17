---
title: Чтение и запись данных HBase с помощью Spark в Azure HDInsight
description: Чтение и запись данных из кластера Spark в кластер HBase с помощью соединителя Spark HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.openlocfilehash: e747f39ca84bb859b37550efef51e01cffd96876
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056747"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Чтение и запись данных Apache HBase с помощью Apache Spark

Обычно для запроса Apache HBase применяется низкоуровневый API (сканирует, получает и помещает) или синтаксис SQL, использующий Apache Phoenix. Apache также предоставляет соединитель Apache Spark HBase, который является удобной и эффективной альтернативой запросу и изменению данных, хранимых в HBase.

## <a name="prerequisites"></a>Технические условия

* Два отдельных кластеров HDInsight, развернутых в той же виртуальной сети. HBase, а второй — Spark с менее Spark 2.1 (HDInsight 3.6) установлен. Дополнительные сведения см. в статье [Создание кластеров под управлением Linux в HDInsight с помощью портала Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

* Клиент SSH. Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* [Схема URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) для основного хранилища кластеров. Это было бы wasb: / / для хранилища BLOB-объектов Azure, abfs: / / для Gen2 хранилища Озера данных Azure или adl: / / для Gen1 хранилища Озера данных Azure. Если безопасной передачи включена для хранилища BLOB-объектов или Gen2 хранилища Data Lake, URI будет wasbs: / / или abfss: / /, соответственно см. также [безопасное перемещение](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Общий процесс

Ниже приведен общий процесс, чтобы позволить кластеру Spark запрашивать кластер HDInsight.

1. Подготовка демонстрационных данных в HBase.
2. Извлечение файла hbase-site.xml из папки конфигурации кластера HBase (/etc/hbase/conf).
3. Помещение копии файла hbase-site.xml в папку конфигурации Spark 2 (/etc/spark2/conf).
4. Запуск `spark-shell` со ссылкой на соединитель Spark HBase по его координатам Maven в параметре `packages`.
5. Определение каталога, который сопоставляет схему из Spark с HBase.
6. Взаимодействие с данными HBase с помощью API RDD или таблицы данных.

## <a name="prepare-sample-data-in-apache-hbase"></a>Подготовка демонстрационных данных в Apache HBase

На этом этапе создания и заполнения таблицы в Apache HBase, который затем можно запросить с помощью Spark.

1. Используйте `ssh` команду, чтобы подключиться к кластеру HBase. Измените указанную ниже команду, заменив `HBASECLUSTER` именем HBase кластера, а затем введите команду:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Используйте `hbase shell` команду, чтобы запустить интерактивную оболочку HBase. В строку SSH-подключения введите следующую команду:

    ```bash
    hbase shell
    ```

3. Используйте `create` команду, чтобы создать таблицу HBase с двумя семействами столбцов. Введите следующую команду:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Используйте `put` команду, чтобы вставить значения в указанный столбец в указанной строке в определенной таблице. Введите следующую команду:

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

5. Используйте `exit` команду, чтобы остановить интерактивную оболочку HBase. Введите следующую команду:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Скопируйте файл hbase-site.xml для кластера Spark

Скопируйте файл hbase-site.xml из локального хранилища в корень хранилища по умолчанию для кластера Spark.  Измените следующую команду, чтобы они соответствовали конфигурации.  Затем откройте сеанс SSH к кластеру HBase, введите команду:

| Тип данных | Новое значение|
|---|---|
|[Схема URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Измените в соответствии с хранилищем.  В представленном ниже синтаксисе предназначен для хранилища BLOB-объектов с включенной безопасной передачей.|
|`SPARK_STORAGE_CONTAINER`|Замените имя контейнера хранилища по умолчанию для кластера Spark.|
|`SPARK_STORAGE_ACCOUNT`|Замените имя учетной записи хранения по умолчанию для кластера Spark.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Выйдите из вашей ssh подключение к кластеру HBase.

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Помещение файла hbase-site.xml в кластер Spark

1. Подключитесь к головному узлу кластера Spark с помощью SSH.

2. Введите следующую команду, чтобы скопировать `hbase-site.xml` из хранилища по умолчанию кластера Spark в папку конфигурации Spark 2 в локальном хранилище кластера:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Запуск оболочки Shell со ссылкой на соединитель Spark HBase

1. Откройте сеанс SSH для кластера Spark введите следующую команду, чтобы запустить оболочку spark:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Оставьте открытым экземпляр оболочки Spark и перейдите к следующему шагу.

## <a name="define-a-catalog-and-query"></a>Определение каталога и отправка запроса

На этом этапе вы определяете объект каталога, который сопоставляет схему из Spark с Apache HBase.  

1. В открытой оболочке Spark, введите следующую `import` инструкции:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

2. Введите следующую команду, чтобы определить каталог для таблицы контактов, созданных на HBase:

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

    Код выполняет следующие операции:  

     a. Определите схему каталога для таблицы HBase с именем `Contacts`.  
     2\. Определите rowkey как `key` и сопоставьте имена столбцов, используемые в Spark, с семейством столбцов, именем столбца и типом столбца, используемыми в HBase.  
     c. rowkey также должен быть определен как именованный столбец (`rowkey`), который содержит определенное семейство столбцов `cf` из `rowkey`.  

3. Введите следующую команду, чтобы определить метод, который предоставляет таблицу данных для вашей `Contacts` таблицы в HBase:

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
    val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
    query.show()
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

6. Закройте оболочки spark, введя следующую команду:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Дальнейшие действия

* [Соединитель Apache Spark HBase](https://github.com/hortonworks-spark/shc)
