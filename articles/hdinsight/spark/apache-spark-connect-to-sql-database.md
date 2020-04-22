---
title: Используйте Apache Spark для чтения и записи данных в базу данных Azure S'L
description: Узнайте, как настроить соединение между кластером HDInsight Spark и базой данных Azure S'L. Для чтения данных, записи данных и потоковой передачи данных в базу данных S'L
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/20/2020
ms.openlocfilehash: 4e783a233bd35e012c02fbbbdc7f4223552fc734
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686852"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Используйте кластер HDInsight Spark для чтения и записи данных в базу данных Azure S'L

Узнайте, как подключить кластер Apache Spark в Azure HDInsight с базой данных Azure S'L. Затем читайте, пишите и перекачийте данные в базу данных S'L. Инструкции в этой статье используют записную книжку Jupyter для запуска фрагментов кода Scala. Тем не менее, вы можете создать отдельное приложение в Scala или Python и выполнять те же задачи.

## <a name="prerequisites"></a>Предварительные требования

* Кластер Azure HDInsight Spark.  Инструкции см. в статье [Создание кластера Apache Spark в Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* База данных SQL Azure. Следуйте инструкциям по [созданию базы данных Azure S'L.](../../sql-database/sql-database-get-started-portal.md) Создайте базу данных с примером схемы и данными **AdventureWorksLT**. Создайте также правило брандмауэра на уровне сервера, чтобы предоставить клиентским IP-адресам доступ к базе данных SQL на сервере. Инструкции по добавлению правила брандмауэра находятся в той же статье. После создания базы данных Azure S'L убедитесь, что вы держите следующие значения под рукой. Они понадобятся вам для подключения к базе данных из кластера Spark.

    * Имя сервера, сохотражая базу данных Azure S'L.
    * Имя базы данных Azure S'L.
    * Имя пользователя / пароль admin-администрирования лазурной базы данных S'L.

* Студия управления серверами S'L (SSMS). Инструкции см. в статье [Подключайтесь к базе данных Azure SQL и создавайте запросы к ней с помощью SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Создание записной книжки Jupyter

Начните с создания ноутбука Jupyter, связанного с кластером Spark. Она понадобится для выполнения фрагментов кода, используемых в этой статье.

1. Откройте кластер на [портале Azure](https://portal.azure.com/).
1. Выберите **Записная книжка Jupyter** в разделе **Панели мониторинга кластера** справа.  Если вы не видите **панели мониторинга кластера,** выберите **Обзор** из левого меню. При появлении запроса введите учетные данные администратора для кластера.

    ![Jupyter ноутбук на Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Jupyter ноутбук на Spark")

   > [!NOTE]  
   > Вы также можете получить доступ к записной книжке Jupyter в кластере Spark, открыв следующий URL-адрес в браузере. Замените **CLUSTERNAME** именем кластера:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Чтобы создать записную книжку Scala, в записной книжке Jupyter в правом верхнем углу щелкните **New** (Создать), а затем — **Spark**. Записные книжки Jupyter в кластере HDInsight Spark предоставляют ядро **PySpark** для приложений Python2 и ядро **PySpark3** для приложений Python3. В этой статье мы создаем записную книжку Scala.

    ![Ядра для ноутбука Jupyter на Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Ядра для ноутбука Jupyter на Spark")

    Дополнительные сведения о ядрах см. в статье [Ядра для записных книжек Jupyter с кластерами Apache Spark в HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > В этой статье используется ядро Spark (Scala), так как в настоящее время потоковая передача данных из Spark в базу данных SQL поддерживается только на Scala и Java. Несмотря на то что чтение и запись в SQL можно выполнить с помощью Python, для обеспечения согласованности в этой статье мы используем Scala для всех трех операций.

1. Новый блокнот открывается с именем по умолчанию, **Без названия**. Щелкните это имя и введите имя по своему усмотрению.

    ![Указание имени для записной книжки](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Указание имени для записной книжки")

Теперь можно приступить к созданию приложения.

## <a name="read-data-from-azure-sql-database"></a>Прочитайте данные из базы данных Azure S'L

В этом разделе вы выполняете операцию чтения данных из таблицы (например, **SalesLT.Address**), которая находится в базе данных AdventureWorks.

1. В новом блокноте Jupyter в ячейке кода вставьте следующий фрагмент и замените значения заполнителя значениями для базы данных Azure S'L.

    ```scala
    // Declare the values for your Azure SQL database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Чтобы выполнить ячейку кода, нажмите клавиши **SHIFT+ВВОД**.  

1. Используйте фрагмент ниже, чтобы создать URL-адрес JDBC, который можно перейти к ApIs данных Spark data. Код создает `Properties` объект для удержания параметров. Вставьте фрагмент кода в ячейку кода и нажмите клавиши **SHIFT+ВВОД**, чтобы выполнить код.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Используйте фрагмент ниже, чтобы создать кадр данных с данными из таблицы в базе данных Azure S'L. В этом фрагменте мы `SalesLT.Address` используем таблицу, которая доступна как часть базы данных **AdventureWorksLT.** Вставьте фрагмент кода в ячейку кода и нажмите клавиши **SHIFT+ВВОД**, чтобы выполнить код.

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. Теперь можно выполнять операции на кадре данных, такие как получение схемы данных:

    ```scala
    sqlTableDF.printSchema
    ```

    Вы видите выход, похожий на следующее изображение:

    ![выход схемы](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "выход схемы")

1. Вы также можете делать операции, как, получить верхние 10 строк.

    ```scala
    sqlTableDF.show(10)
    ```

1. Можно также извлечь определенные столбцы из набора данных.

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Запись данных в базу данных Azure S'L

В этом разделе мы используем пример файла CSV, доступного в кластере, для создания таблицы в базе данных Azure S'L и заполнения ее данными. Пример CSV-файла (**HVAC.csv**) содержится во всех кластерах HDInsight в папке `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. В новом блокноте Jupyter в ячейке кода вставьте следующий фрагмент и замените значения заполнителя значениями для базы данных Azure S'L.

    ```scala
    // Declare the values for your Azure SQL database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Чтобы выполнить ячейку кода, нажмите клавиши **SHIFT+ВВОД**.  

1. Следующий фрагмент создает URL-адрес JDBC, который можно перейти на AIS данных Spark data. Код создает `Properties` объект для удержания параметров. Вставьте фрагмент кода в ячейку кода и нажмите клавиши **SHIFT+ВВОД**, чтобы выполнить код.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. В приведенном ниже фрагменте кода извлекается схема данных в HVAC.csv и с ее помощью данные загружаются из CSV-файла в кадр данных `readDf`. Вставьте фрагмент кода в ячейку кода и нажмите клавиши **SHIFT+ВВОД**, чтобы выполнить код.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Используя кадр данных `readDf`, создайте временную таблицу `temphvactable`. Затем с ее помощью создайте таблицу hive `hvactable_hive`.

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. Наконец, используйте таблицу улья для создания таблицы в базе данных Azure S'L. Следующий фрагмент создается `hvactable` в базе данных Azure S'L.

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. Подключитесь к базе данных Azure S'L `dbo.hvactable` с помощью SSMS и убедитесь, что вы видите его.

    а. Запустите SSMS и подключитесь к базе данных Azure S'L, предоставив сведения о подключении, показанные на скриншоте ниже.

    ![Подключение к базе данных S'L с помощью SSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Подключение к базе данных S'L с помощью SSMS1")

    b. От **Object Explorer**расширьте базу данных Azure S'L и узел таблицы, чтобы увидеть созданный **dbo.hvactable.**

    ![Подключение к базе данных S'L с помощью SSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Подключение к базе данных S'L с помощью SSMS2")

1. Выполните запрос в SSMS для просмотра всех столбцов в таблице.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Потоковые данные в базу данных Azure S'L

В этом разделе мы переплетаданные данные в базу данных, `hvactable` созданную уже в базе данных Azure S'L в предыдущем разделе.

1. В качестве первого шага убедитесь, `hvactable`что в . С помощью SSMS выполните следующий запрос к таблице.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Создайте записную книжку Jupyter в кластере HDInsight Spark. Вставьте следующий фрагмент кода в ячейку кода и нажмите клавиши **SHIFT+ВВОД**.

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. Мы перевостричиваем данные `hvactable`из **HVAC.csv** в . Файл HVAC.csv доступен в `/HdiSamples/HdiSamples/SensorSampleData/HVAC/`кластере по адресу . В следующем фрагменте кода мы сначала получаем схему данных для потоковой передачи. Затем создаем кадр данных потоковой передачи с помощью этой схемы. Вставьте фрагмент кода в ячейку кода и нажмите клавиши **SHIFT+ВВОД**, чтобы выполнить код.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. Выходные данные будут содержать схему файла **HVAC.csv**. Имеет `hvactable` ту же схему, а также. В выходных данных перечислены столбцы в таблице.

    !['hdinsight Apache Spark схема таблица'](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Схема стола")

1. Наконец, используйте следующий фрагмент для чтения данных из HVAC.csv и потоковой передачи данных `hvactable` в базу данных Azure S'L. Вставьте фрагмент в ячейку кода, замените значения заполнителя значениями для базы данных Azure S'L, а затем нажмите **SHIFT и ENTER** для запуска.

    ```scala
    val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
        var connection:java.sql.Connection = _
        var statement:java.sql.Statement = _

        val jdbcUsername = "<SQL DB ADMIN USER>"
        val jdbcPassword = "<SQL DB ADMIN PWD>"
        val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
        val jdbcPort = 1433
        val jdbcDatabase ="<AZURE SQL DB NAME>"
        val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"

        def open(partitionId: Long, version: Long):Boolean = {
        Class.forName(driver)
        connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
        statement = connection.createStatement
        true
        }

        def process(value: Row): Unit = {
        val Date  = value(0)
        val Time = value(1)
        val TargetTemp = value(2)
        val ActualTemp = value(3)
        val System = value(4)
        val SystemAge = value(5)
        val BuildingID = value(6)  

        val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
        statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")
        }

        def close(errorOrNull: Throwable): Unit = {
        connection.close
        }
        })

    var streamingQuery = WriteToSQLQuery.start()
    ```

1. Проверить, что данные передаются `hvactable` в потоковом, запустив следующий запрос в студии управления серверами S'L (SSMS). При каждом выполнении запроса отображется большее количество строк в таблице.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Следующие шаги

* [Использование кластера HDInsight Spark для анализа данных в Data Lake Storage](apache-spark-use-with-data-lake-store.md)
* [Загрузка данных и выполнение запросов в кластере Apache Spark в Azure HDInsight](apache-spark-load-data-run-query.md)
* [Использование структурированной потоковой передачи Apache Spark с Apache Kafka в HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
