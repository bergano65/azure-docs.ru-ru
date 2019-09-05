---
title: Используйте соединитель Azure обозреватель данных для Apache Spark перемещения данных между кластерами обозреватель данных и Spark Azure.
description: В этом разделе показано, как перемещать данные между кластерами Azure обозреватель данных и Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 6a95cbad161906bd12a608880ac694d6bdf1ed27
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383052"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Соединитель Azure обозреватель данных для Apache Spark (Предварительная версия)

[Apache Spark](https://spark.apache.org/) — это единый модуль аналитики для обработки больших данных. Azure обозреватель данных — это быстрая, полностью управляемая служба аналитики данных для анализа больших объемов данных в режиме реального времени. 

Соединитель Azure обозреватель данных для Spark реализует источник данных и приемник данных для перемещения данных между кластерами Azure обозреватель данных и Spark для использования обеих возможностей. При использовании Azure Data Explorer и Apache Spark вы можете выполнять сборку быстрых и масштабируемых приложений, ориентированных на управляемые данными сценарии, такие как машинное обучение, сценарий извлечения, преобразования и загрузки (ETL) и Log Analytics. Запись в обозреватель данных Azure может выполняться в пакетном и потоковой режимах.
Чтение из Azure обозреватель данных поддерживает очистку столбцов и предикаты включение, что сокращает объем передаваемых данных путем фильтрации данных в обозреватель данных Azure.

Соединитель Azure обозреватель данных Spark — это [проект с открытым исходным кодом](https://github.com/Azure/azure-kusto-spark) , который может работать в любом кластере Spark. Соединитель Azure обозреватель данных Spark делает Azure обозреватель данных допустимым хранилищем данных для стандартных операций источника и приемника Spark, таких как Write, Read и Вритестреам. 

> [!NOTE]
> Хотя некоторые из приведенных ниже примеров относятся к кластеру [Azure Databricks](https://docs.azuredatabricks.net/) Spark, соединитель Azure обозреватель данных Spark не принимает прямые зависимости от кирпичей данных или других дистрибутивов Spark.

## <a name="prerequisites"></a>Предварительные требования

* [Создайте кластер и базу данных Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal). 
* Создание кластера Spark
* Установите библиотеку соединителей Azure обозреватель данных и библиотеки, перечисленные в списке [зависимости](https://github.com/Azure/azure-kusto-spark#dependencies) , включая следующие библиотеки [SDK Kusto для Java](/azure/kusto/api/java/kusto-java-client-library) :
    * [Клиент данных Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Принимающий клиент Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Предварительно созданные библиотеки для [Spark 2,4, Scala 2,11](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>Как создать соединитель Spark

Соединитель Spark можно построить из [источников](https://github.com/Azure/azure-kusto-spark) , как описано ниже.

> [!NOTE]
> Этот шаг не является обязательным. Если вы используете предварительно созданные библиотеки, перейдите в раздел [Установка кластера Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Необходимые компоненты для сборки

* Установлен пакет SDK для Java 1,8
* Установлен [Maven 3. x](https://maven.apache.org/download.cgi)
* Apache Spark версии 2.4.0 или выше

> [!TIP]
> версии 2.3. x также поддерживаются, но может потребоваться внести некоторые изменения в зависимости POM. XML.

Для приложений Scala/Java, использующих определения проектов Maven, свяжите приложение со следующим артефактом (последняя версия может отличаться):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>Команды сборки

Для создания JAR-файла и выполнения всех тестов выполните команду:

```
mvn clean package
```

Чтобы создать JAR-файл, выполните все тесты и установите JAR-файл в локальный репозиторий Maven:

```
mvn clean install
```

Дополнительные сведения см. в разделе [Использование соединителя](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Установка кластера Spark

> [!NOTE]
> При выполнении следующих действий рекомендуется использовать последнюю версию соединителя Azure обозреватель данных Spark.

1. Настройте следующие параметры кластера Spark на основе кластера Azure Databricks, используя Spark 2,4 и Scala 2,11: 

    ![Параметры кластера "кирпичные данные"](media/spark-connector/databricks-cluster.png)

1. Импортируйте библиотеку соединителя обозреватель данных Azure:

    ![Импорт библиотеки обозреватель данных Azure](media/spark-connector/db-create-library.png)

1. Добавление дополнительных зависимостей (необязательно, если используется из Maven):

    ![Добавление зависимостей](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > Правильная версия выпуска Java для каждого выпуска Spark находится [здесь](https://github.com/Azure/azure-kusto-spark#dependencies).

1. Убедитесь, что установлены все необходимые библиотеки:

    ![Проверка установленных библиотек](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Проверка подлинности

Соединитель Azure обозреватель данных Spark позволяет проходить проверку подлинности в Azure Active Directory (Azure AD) с помощью [приложения Azure AD](#azure-ad-application-authentication), [маркера доступа Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [проверки подлинности устройства](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (для нерабочих сценариев) или [Azure Key. Хранилище](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). Пользователь должен установить пакет Azure-keyvault и предоставить учетные данные приложения для доступа к ресурсу Key Vault.

### <a name="azure-ad-application-authentication"></a>Проверка подлинности приложения Azure AD

Наиболее простой и распространенный метод проверки подлинности. Этот метод рекомендуется для использования соединителя Azure обозреватель данных Spark.

|Свойства  |Описание  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Идентификатор приложения Azure AD (клиент).      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Центр проверки подлинности Azure AD. Идентификатор каталога Azure AD (клиент).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Ключ приложения Azure AD для клиента.     |

### <a name="azure-data-explorer-privileges"></a>Права обозреватель данных Azure

Следующие права должны быть предоставлены в кластере Azure обозреватель данных.

* Для чтения (источник данных) приложение Azure AD должно иметь права доступа *средства просмотра* в целевой базе данных или права *администратора* в целевой таблице.
* Для записи (приемника данных) приложение Azure AD должно иметь привилегии *приема* в целевой базе данных. Для создания новых таблиц у нее также должны быть привилегии *пользователя* в целевой базе данных. Если целевая таблица уже существует, можно настроить права *администратора* в целевой таблице.
 
Дополнительные сведения о ролях участников Azure обозреватель данных см. в разделе [авторизация на основе ролей](/azure/kusto/management/access-control/role-based-authorization). Сведения об управлении ролями безопасности см. в разделе [Управление ролями безопасности](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Приемник Spark: Запись в обозреватель данных Azure

1. Настройка параметров приемника:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Запись данных Spark в кластер Azure обозреватель данных в качестве пакета:

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    val conf = Map(
            KustoSinkOptions.KUSTO_CLUSTER -> cluster,
            KustoSinkOptions.KUSTO_TABLE -> table,
            KustoSinkOptions.KUSTO_DATABASE -> database,
            KustoSinkOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId)
    
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .options(conf)
      .save()
      
    ```
    
   Или используйте упрощенный синтаксис:
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. Запись потоковых данных:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false") // Use in case a NullPointerException is thrown inside codegen iterator
    
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Источник Spark: Чтение из обозреватель данных Azure

1. При чтении небольших объемов данных определите запрос данных:

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. При чтении больших объемов данных необходимо указать временное хранилище BLOB-объектов. Укажите ключ SAS контейнера хранилища, имя учетной записи хранения, ключ учетной записи и имя контейнера. Этот шаг требуется только для текущего предварительного выпуска соединителя Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    В приведенном выше примере нет доступа к Key Vault с помощью интерфейса соединителя. Кроме того, мы используем более простой метод использования секретов блоков данных.

1. Чтение из обозреватель данных Azure:

    ```scala
     val conf3 = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
          KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```
